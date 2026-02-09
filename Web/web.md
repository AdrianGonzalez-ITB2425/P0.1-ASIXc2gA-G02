# Extagram – Presentació, Explicació Tècnica i Conclusió  
*(Documentació de la part web)*

## 1. Presentació de l'Aplicació Web

### 1.1. Descripció General

Extagram és una aplicació web tipus xarxa social que permet als usuaris **publicar textos i adjuntar-hi imatges**, mostrant-los posteriorment en un mur de publicacions. L'objectiu principal és oferir una plataforma senzilla però funcional per compartir contingut, mantenint alhora una arquitectura tècnica pròxima a entorns de producció reals.

L'aplicació està desenvolupada en **PHP** i utilitza una base de dades **MySQL** per emmagatzemar les publicacions. El desplegament s'ha realitzat mitjançant **Docker** i **Docker Compose**, dividint la solució en diversos serveis especialitzats (proxy invers, servidors PHP, servidor d'upload, servidor d'imatges, servidor d'estàtics i base de dades).

### 1.2. Objectius Funcional i Tècnic

A nivell funcional, la web ha de permetre:

- Crear noves publicacions amb text i imatge opcional.  
- Visualitzar les publicacions recents en un mur.  
- Gestionar les publicacions des d'un panell d'administració.

A nivell tècnic, la plataforma busca:

- **Alta disponibilitat** mitjançant la duplicació de servidors PHP (S2 i S3) i el balanceig de càrrega.  
- **Escalabilitat horitzontal**, afegint instàncies addicionals quan sigui necessari.  
- **Separació clara de rols** (proxy, lògica, estàtics, base de dades) segons el paradigma de microserveis.  
- **Desplegament en un entorn de núvol** amb una arquitectura simple però realista.

---

## 2. Arquitectura Web i Components Principals

### 2.1. Visió Global

L'arquitectura web d'Extagram es basa en **set serveis** independents, executats com a contenidors Docker, interconnectats mitjançant dues xarxes de tipus *bridge*:

- **extanet**: xarxa "pública" que connecta el proxy invers amb els servidors web i de fitxers.  
- **datanet**: xarxa "privada" que connecta únicament els servidors PHP amb la base de dades MySQL.

L'entrada al sistema es fa sempre a través de **S1 (NGINX – proxy invers)**, que distribueix les peticions cap als diferents serveis segons el tipus de recurs sol·licitat (pàgines PHP, imatges, CSS/SVG, etc.).

### 2.2. Descripció dels Serveis Web

#### 2.2.1. S1 – Proxy Invers NGINX

- **Funció principal:** punt únic d'entrada per a totes les peticions HTTP/HTTPS.  
- **Tasques destacades:**
  - Redirecció automàtica de **HTTP → HTTPS**.  
  - Gestió del **certificat SSL** de Let's Encrypt.  
  - **Balanceig de càrrega** amb l'algorisme `ip_hash` entre els servidors PHP S2 i S3.  
  - Enrutament de peticions segons el camí:
    - Arxius estàtics (`.css`, `.svg`, imatges, icones) cap a **S6**.  
    - Rutes `/uploads/` cap a **S5** (servidor d'imatges).  
    - `upload.php` cap a **S4** (servidor d'upload).  
    - Altres fitxers `.php` (com `extagram.php`, `admin.php`, `delete.php`) cap al *backend* balancejat (`php_backend` → S2/S3).

Aquest disseny garanteix que el client només vegi un únic host (extagram-g2.duckdns.org), mentre que la lògica interna de distribució queda totalment encapsulada al proxy.

#### 2.2.2. S2 i S3 – Servidors PHP-FPM (Lògica Principal)

- **Funció principal:** executar la lògica d'aplicació de la part web.  
- **Fitxers principals:**
  - `extagram.php`: pàgina principal amb el formulari de creació i el mur de publicacions.  
  - `admin.php`: panell d'administració per a la gestió de posts.  
  - `delete.php`: gestió de l'esborrat de publicacions i imatges associades.

Aquests dos contenidors comparteixen el mateix *volume* amb el codi PHP i reben les peticions balancejades des de S1. Això proporciona:

- **Redundància:** si un dels servidors falla, l'altre continua donant servei.  
- **Repartiment de càrrega:** les peticions es reparteixen de forma equitativa entre S2 i S3.

#### 2.2.3. S4 – Servidor d'Upload (PHP-FPM)

- **Funció principal:** processar exclusivament les pujades d'imatges a través de `upload.php`.  
- **Procés resumit:**
  1. Recepció del formulari amb el text de la publicació i el fitxer d'imatge.  
  2. Validació de l'estat del fitxer i del tipus MIME.  
  3. Generació d'un nom únic per a la imatge (`uniqid()` + extensió).  
  4. Moviment físic del fitxer al directori compartit `uploads/`.  
  5. Inserció d'un nou registre a la taula `posts` de MySQL amb el text i la ruta de la imatge.  
  6. Redirecció de l'usuari a la pàgina principal informant de l'èxit de l'operació.

Aquest servei es troba separat de S2/S3 per evitar que les operacions de càrrega, potencialment més lentes, afectin el rendiment del processament normal de pàgines.

#### 2.2.4. S5 – Servidor d'Imatges (NGINX)

- **Funció principal:** servir les imatges emmagatzemades al directori `uploads/`.  
- **Característiques:**
  - Utilitza NGINX per optimitzar la transferència de fitxers estàtics.  
  - Comparteix un volum amb S4 per accedir a totes les imatges pujades.  
  - És el destí de totes les rutes `/uploads/...` definides a la configuració de S1.

#### 2.2.5. S6 – Servidor d'Estàtics (NGINX)

- **Funció principal:** servir els fitxers estàtics de la interfície (principalment `style.css`, `preview.svg` i `favicon.ico`).  
- **Optimització:** s'aplica una política de **caché de 7 dies** per reduir el nombre de peticions repetitives, millorant el rendiment de la càrrega de la web.

#### 2.2.6. S7 – Base de Dades MySQL

- **Funció principal:** emmagatzematge persistent de les publicacions.  
- **Esquema principal (`posts`):**
  - `id` (INT, clau primària, auto-increment).  
  - `post` (TEXT, contingut textual de la publicació).  
  - `photourl` (VARCHAR, ruta relativa de la imatge associada).  
  - `created_at` (TIMESTAMP, data i hora de creació).

Aquest servei està únicament connectat a la xarxa **datanet**, fet que impedeix l'accés directe des de l'exterior i limita les connexions a S2, S3 i S4.

---

## 3. Funcionament Funcional de la Web

### 3.1. Experiència d'Usuari a la Pàgina Principal

En accedir a `https://extagram-g2.duckdns.org/`, l'usuari troba:

- Una **secció principal (hero)** amb un missatge de benvinguda i una crida a l'acció per començar a publicar.  
- Un **formulari de creació de publicacions** que inclou:
  - Selecció d'imatge amb **vista prèvia** (preview) mitjançant JavaScript.  
  - Àrea de text amb **límit de caràcters** i comptador en temps real.  
  - Botó de "Publicar" que envia el formulari a `upload.php`.  
- Un apartat de **"Últimes publicacions"** on es mostra el mur de posts.

La interfície ofereix, a més, **tres modes de visualització** del mur (grid, vertical i horitzontal), seleccionables per l'usuari mitjançant botons que alteren la disposició dels elements via JavaScript i CSS.

### 3.2. Sistema Multiidioma

La web incorpora un **sistema bàsic d'internacionalització** basat en sessions PHP:

- Per defecte es carrega l'idioma castellà (`es`).  
- L'usuari pot alternar entre **espanyol** i **anglès** mitjançant un botó a la capçalera.  
- Les cadenes de text (títol, placeholders, etiquetes de botons, etc.) es gestionen mitjançant un array associatiu PHP, la qual cosa facilita l'extensió futura a altres idiomes.

### 3.3. Panell d'Administració

La ruta `/admin.php` proporciona un **panell d'administració** protegit per credencials:

- Autenticació via formulari (usuari i contrasenya).  
- Emmagatzematge de l'estat de sessió en `$_SESSION['admin_logged']`.  
- Un cop validat, l'administrador pot:
  - Visualitzar **estadístiques bàsiques** (nombre total de posts i posts amb imatge).  
  - Consultar un **llistat complet de publicacions** amb miniatures.  
  - Eliminar publicacions de forma individual mitjançant un formulari que fa POST a `delete.php`.

En eliminar una publicació, el sistema elimina tant el registre de la base de dades com el fitxer físic de la imatge del directori `uploads/`, mantenint la coherència entre la capa d'aplicació i la capa d'emmagatzematge.

---

## 4. Explicació Tècnica Detallada

### 4.1. Flux de Dades en una Càrrega Normal de la Pàgina

1. L'usuari accedeix a `https://extagram-g2.duckdns.org/`.  
2. S1 rep la petició, valida el host i la ruta i la dirigeix a un dels servidors PHP (S2 o S3) responsable d'executar `extagram.php`.  
3. `extagram.php`:
   - Inicia la sessió i determina l'idioma.  
   - Estableix connexió amb S7 (MySQL) mitjançant l'extensió MySQLi.  
   - Executa una consulta `SELECT` ordenada per `id` descendent per obtenir les últimes publicacions.  
   - Genera la sortida HTML amb les publicacions i les rutes d'imatge corresponents.  
4. El navegador del client, un cop rep l'HTML, sol·licita:
   - `style.css` → S1 el redirigeix cap a S6.  
   - Imatges `/uploads/...` → S1 les redirigeix cap a S5.  
5. El resultat és una pàgina renderitzada amb els últims posts i els estils aplicats.

### 4.2. Flux de Dades en Crear una Nova Publicació

1. L'usuari emplena el formulari a la secció "Crear nova publicació" i, opcionalment, selecciona una imatge.  
2. En prémer "Publicar", el formulari fa un **POST** amb `multipart/form-data` a la ruta `/upload.php`.  
3. S1 identifica la ruta i deriva la petició cap a S4 (servidor d'upload).  
4. `upload.php`:
   - Comprova que el fitxer s'ha rebut correctament i que no hi ha errors (`UPLOAD_ERR_OK`).  
   - Obté l'extensió de l'arxiu i genera un nom únic per evitar col·lisions.  
   - Utilitza `move_uploaded_file()` per desar la imatge al directori compartit `uploads/`.  
   - Obre una connexió a MySQL (S7) i executa una instrucció `INSERT INTO posts (post, photourl)` utilitzant **consultes preparades** (`prepare`, `bind_param`, `execute`) per prevenir injeccions SQL.  
   - Finalment, realitza una redirecció `Location: https://extagram-g2.duckdns.org/?success=1`.

5. En tornar a carregar `extagram.php`, la nova publicació ja apareix al mur, carregada des de la base de dades.

### 4.3. Consideracions de Seguretat

La implementació incorpora diverses mesures de seguretat importants:

- **Xifrat de comunicacions:** ús de HTTPS amb certificat de Let's Encrypt, forçant redirecció des de HTTP.  
- **Prevenció d'injeccions SQL:** ús sistemàtic de consultes preparades i vinculació de paràmetres (`bind_param`).  
- **Sanitització de sortides:** ús de `htmlspecialchars()` i `nl2br()` per mostrar el contingut dels posts, evitant així l'execució de codi HTML o JavaScript injectat.  
- **Validació de fitxers:** comprovació de l'estat de l'upload i del tipus de fitxer abans de desar-lo.  
- **Segregació de xarxa:** la base de dades MySQL (S7) només és accessible des dels contenidors PHP connectats a la xarxa `datanet`.  
- **Control d'accés a l'administració:** protecció del panell d'administració mitjançant usuari, contrasenya i control de sessió.

---

## 5. Conclusions

### 5.1. Avaluació de la Solució Web

La part web d'Extagram compleix els objectius plantejats tant a nivell funcional com a nivell d'arquitectura:

- Ofereix una **experiència d'usuari completa** per crear i visualitzar publicacions amb imatge.  
- Proporciona un **panell d'administració** senzill però efectiu per gestionar el contingut.  
- Implementa una **arquitectura modular** basada en microserveis, amb segregació de responsabilitats i capacitat de **balanceig de càrrega**.  
- Integra bones pràctiques de **seguretat** (HTTPS, SQL segur, sanitització, xarxes privades) i de **persistència** (volums per a la base de dades i les imatges).

### 5.2. Punts Forts

- **Escalabilitat horitzontal:** la duplicació dels servidors PHP (S2 i S3) i la possibilitat d'afegir-ne de nous facilita el creixement de l'aplicació davant d'un augment de trànsit.  
- **Alta disponibilitat:** l'ús d'un proxy invers amb balanceig de càrrega permet mantenir el servei actiu fins i tot si un dels nodes PHP deixa de funcionar.  
- **Arquitectura clara i documentable:** la divisió S1–S7 reflecteix una separació de rols entenedora i alineada amb arquitectures web reals.

### 5.3. Possibles Línies de Millora

De cara a futurs sprints o al projecte P0.2, es podrien considerar:

- Afegir **monitorització** i alertes (per exemple, amb Prometheus i Grafana) per controlar l'estat dels serveis.  
- Implementar còpies de seguretat automatitzades de la base de dades i del directori `uploads/`.  
- Incorporar mesures de **seguretat addicionals** (rate limiting, filtres WAF, millora de la política de contrasenyes).  
- Refactoritzar part del codi PHP a un patró més estructurat (MVC o similars) per facilitar el manteniment a llarg termini.

---

En conjunt, la web d'Extagram representa una implementació sòlida d'una aplicació PHP moderna sobre Docker, amb una arquitectura clara, bones pràctiques bàsiques de seguretat i una funcionalitat plenament operativa per a l'usuari final i per a l'administrador del sistema.