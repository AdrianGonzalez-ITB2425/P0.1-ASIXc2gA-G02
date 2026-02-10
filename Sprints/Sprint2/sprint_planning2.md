# ACTA DE REUNIÓ - SPRINT PLANNING (SPRINT 2)

**Projecte:** Extagram G2  
**Data:** 20 de gener de 2026  
**Hora:** 15:00 h  

---

## ASSISTENTS

Javier  
Adrián  
Marc  

---

## OBJECTIU DEL SPRINT

L'objectiu fonamental d'aquest segon sprint és la **dockerització completa de l'aplicació**. Es pretén migrar l'arquitectura actual a un sistema de microserveis (contenidors) orquestrats. A més, s'implementaran millores crítiques de seguretat i funcionalitats administratives al backend.

---

## SPRINT BACKLOG (TASQUES PLANIFICADES)

### 1. Infraestructura i Orquestració (Docker)

**Instal·lar Docker en AWS** Preparació de la instància EC2 per suportar la nova arquitectura de contenidors.  
*Responsable:* Javier.  
*Data límit:* 2 de febrer.

**S8: Docker Compose Complet** Creació i configuració del fitxer `docker-compose.yml` per orquestrar tots els serveis.  
*Responsable:* Javier.  
*Data límit:* 2 de febrer.

**S1: Nginx Proxy Invers + Balancejador** Configuració del punt d'entrada principal per gestionar el trànsit cap als diferents contenidors.  
*Responsables:* Adrián, Marc i Javier.  
*Data límit:* 2 de febrer.

**Comprovar funcionalitat de la nova màquina** Validació final del desplegament amb la configuració actual.  
*Responsables:* Adrián, Marc i Javier.

### 2. Contenidors de Serveis i Backend

**S2 y S3: PHP-FPM (extagram.php)** Configuració del contenidor per gestionar la lògica principal de l'aplicació.  
*Responsables:* Adrián i Marc.  
*Data límit:* 27 de gener.

**S4: PHP-FPM (upload.php)** Contenidor específic o configuració per a la gestió de pujada d'arxius.  
*Responsable:* Marc.  
*Data límit:* 2 de febrer.

**S7: MySQL Base de Dades** Migració de la base de dades a un contenidor aïllat i persistent.  
*Responsable:* Marc.  
*Data límit:* 27 de gener.

### 3. Servidor Web i Estàtics

**S6: Nginx Arxius Estàtics** Configuració optimitzada per servir CSS i JS de manera eficient.  
*Responsable:* Adrián.  
*Data límit:* 2 de febrer.

**S5: Nginx Servidor d'Imatges** Gestió específica per al lliurament de les imatges pujades pels usuaris.  
*Responsables:* Marc i Javier.  
*Data límit:* 2 de febrer.

### 4. Funcionalitats i Gestió

**Afegir panell d'admin per poder eliminar posts** Desenvolupament de la interfície d'administració al backend.  
*Responsables:* Adrián i Marc.  
*Data límit:* 21 de gener.

**Afegir seguretat a la web** Implementació de mesures de seguretat (SSL/HTTPS i restriccions bàsiques).  
*Responsable:* Marc.  
*Data límit:* 20 de gener.
s
**Ordenar ProofHub i Sprint Planning 2** Gestió administrativa de les tasques i documentació del sprint actual.  
*Responsable:* Javier.

---

## ASSIGNACIÓ DE RESPONSABILITATS

| Membre | Focus Principal al Sprint |
| :--- | :--- |
| **Javier** | **Arquitectura:** Gestió d'eines. |
| **Marc** | **Backend i Dades:** Responsable de la Base de Dades, Seguretat web i lògica de PHP (uploads). Suport en panell d'admin. |
| **Adrián** | **Frontend i Serveis Web:** Lideratge en Docker Compose, instal·lació a AWS i configuració del Proxy Invers. Encarregat dels estàtics en Nginx, lògica principal PHP i desenvolupament del panell d'Administració. |

---

## PLANIFICACIÓ TEMPORAL

| Fita | Data |
| :--- | :--- |
| **Inici del Sprint** | 20/01/2026 |
| **Lliurament Admin Panel** | 21/01/2026 |
| **Finalització Base de Dades** | 27/01/2026 |
| **Fi del Sprint (Desplegament Docker)** | 02/02/2026 |

**Riscos i Dependències:**
La complexitat principal recau en la **interconnexió dels contenidors** (xarxes internes de Docker) i la persistència de dades a MySQL. És crític que el `Docker Compose` estigui ben definit perquè els contenidors individuals (S1-S7) funcionin en conjunt.

---

## CONCLUSIÓ

L'equip inicia la fase de dockerització amb l'objectiu de tenir una arquitectura escalable i modular. S'han repartit els serveis (S1-S8) per treballar en paral·lel, convergint en la integració final el 2 de febrer.

---

**Acta redactada:** 20/01/2026 – 15:15 CET  
**Aprovat per:** Equip Extagram G2


[Torna a l'Inici](../../README.md)