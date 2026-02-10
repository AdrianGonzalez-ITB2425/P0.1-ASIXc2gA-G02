# Orquestració de Contenidors (Docker Compose)

L'arxiu `docker-compose.yml` és el nucli de la infraestructura d'**Extagram**. Defineix com es construeixen, configuren i interconnecten els 7 microserveis que componen l'aplicació.

A continuació es detalla la configuració de xarxes, volums i serveis.

---

## Topologia de Xarxa (Networking)

Per garantir la seguretat i l'aïllament de responsabilitats, s'han definit dues xarxes internes de tipus `bridge`:

1.  **`extanet` (Frontend Network):**
    * Connecta el Proxy Invers (S1) amb els serveis web (S2, S3, S4) i els servidors d'estàtics (S5, S6).
    * Permet que Nginx enruti el trànsit HTTP/HTTPS als contenidors corresponents.

2.  **`datanet` (Backend Network):**
    * Xarxa privada per a la comunicació amb la base de dades.
    * **Seguretat:** El contenidor de base de dades (S7) *només* pertany a aquesta xarxa. Això significa que **S1 (Proxy) i el món exterior no tenen accés directe a MySQL**, només els serveis PHP (S2, S3, S4) poden comunicar-se amb ella.

---

## Definició de Serveis

### 1. Porta d'Enllaç (Gateway)
* **Servei:** `s1-proxy`
* **Imatge:** `nginx:alpine`
* **Ports:** Exposa `80` (HTTP) i `443` (HTTPS) a l'exterior.
* **Funció:** Actua com a Proxy Invers i Balancejador de Càrrega. Acaba la connexió SSL i distribueix les peticions als serveis interns.
* **Volums:** Munta els certificats SSL (`/etc/letsencrypt`) i la configuració de rutes (`s1.conf`).

### 2. Lògica d'Aplicació (Backend PHP)
* **Serveis:** `s2-php` i `s3-php`
* **Build:** Construïts des de `php.Dockerfile`.
* **Xarxes:** Connectats a `extanet` (per rebre peticions de S1) i `datanet` (per consultar a S7).
* **Funció:** Executen la lògica principal de la xarxa social (`extagram.php`). Es despleguen dues rèpliques per assegurar alta disponibilitat i repartiment de càrrega.

### 3. Gestor de Pujades (Upload Service)
* **Servei:** `s4-upload`
* **Build:** Construïts des de `php.Dockerfile`.
* **Port Intern:** Exposa el port `9000` (FastCGI) internament.
* **Funció:** Microservei dedicat exclusivament a processar la pujada d'imatges (`upload.php`). Comparteix el volum `./uploads` per guardar els arxius físicament.

### 4. Servidors de Contingut Estàtic (CDN Local)
* **Servei:** `s5-images`
    * **Funció:** Servidor Nginx optimitzat per servir les imatges pujades pels usuaris.
    * **Volum:** Munta `./uploads` en mode lectura per servir les fotos.
* **Servei:** `s6-static`
    * **Funció:** Serveix els fitxers CSS, JS i SVGs del sistema (`style.css`, `logo.svg`).

### 5. Persistència de Dades (Base de Dades)
* **Servei:** `s7-database`
* **Imatge:** `mysql:8`
* **Xarxa:** Exclusiva a `datanet`.
* **Volums:**
    * `./dbdata`: Persistència de les dades de MySQL al host (evita pèrdua de dades en reiniciar).
    * `init.sql`: Script d'inicialització que crea l'estructura de taules en arrencar per primer cop.
* **Variables d'Entorn:** Utilitza injecció de variables (`${...}`) per no incrustar contrasenyes al codi.

---

## Gestió de Volums i Dades

El sistema utilitza **Bind Mounts** (muntatge de carpetes del host) per facilitar el desenvolupament i la persistència:

| Ruta Host | Ruta Contenedor | Propòsit |
| :--- | :--- | :--- |
| `./uploads` | `/var/www/html/uploads` | Compartit entre S2, S3, S4 (escriptura) i S5 (lectura). Magatzem físic de fotos. |
| `./dbdata` | `/var/lib/mysql` | Persistència de la base de dades MySQL. |
| `./etc/letsencrypt`| `/etc/letsencrypt` | Certificats SSL reals generats per Certbot al host. |

---

## Arxiu Complet: `docker-compose.yml`

```yaml
version: "3.9"

services:
  s1-proxy:
    image: nginx:alpine
    container_name: s1-proxy
    ports:
      - "80:80"
      - "443:443"
    volumes:
      - ./s1-proxy/s1.conf:/etc/nginx/conf.d/default.conf:ro
      - /etc/letsencrypt:/etc/letsencrypt:ro
    depends_on:
      - s2-php
      - s3-php
      - s4-upload
      - s5-images
      - s6-static
    networks:
      - extanet

  s2-php:
    build:
      context: .
      dockerfile: php.Dockerfile
    container_name: s2-php
    volumes:
      - ./s2-s3-php:/var/www/html
      - ./uploads:/var/www/html/uploads
    networks:
      - extanet
      - datanet

  s3-php:
    build:
      context: .
      dockerfile: php.Dockerfile
    container_name: s3-php
    volumes:
      - ./s2-s3-php:/var/www/html
      - ./uploads:/var/www/html/uploads
    networks:
      - extanet
      - datanet

  s4-upload:
    build:
      context: .
      dockerfile: php.Dockerfile
    container_name: s4-upload
    volumes:
      - ./s4-upload:/var/www/html
      - ./uploads:/var/www/html/uploads
    networks:
      - extanet
      - datanet
    expose:
      - "9000"

  s5-images:
    image: nginx:alpine
    container_name: s5-images
    volumes:
      - ./s5-images/s5.conf:/etc/nginx/conf.d/default.conf:ro
      - ./uploads:/var/www/html/uploads
    networks:
      - extanet

  s6-static:
    image: nginx:alpine
    container_name: s6-static
    volumes:
      - ./s6-static/s6.conf:/etc/nginx/conf.d/default.conf:ro
      - ./s6-static/static:/usr/share/nginx/html:ro
    networks:
      - extanet

  s7-database:
    image: mysql:8
    container_name: s7-database
    environment:
      - MYSQL_DATABASE=${DB_NAME}
      - MYSQL_USER=${DB_USER}
      - MYSQL_PASSWORD=${DB_PASSWORD}
      - MYSQL_ROOT_PASSWORD=${DB_ROOT_PASSWORD}
    volumes:
      - ./dbdata:/var/lib/mysql
      - ./s7-database/init.sql:/docker-entrypoint-initdb.d/init.sql:ro
    networks:
      - datanet

networks:
  extanet:
    driver: bridge
  datanet:
    driver: bridge