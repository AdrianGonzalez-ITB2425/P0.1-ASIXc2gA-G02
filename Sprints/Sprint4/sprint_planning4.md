 ACTA DE REUNIÓ - SPRINT PLANNING (SPRINT 4)

**Projecte:** Extagram G2  
**Data:** 17 de febrer de 2026  
**Hora:** 15:00 h 

---

## ASSISTENTS

Adrián González  
Javier Vericat  
Marc Manzorro  

---

## OBJECTIU DEL SPRINT

L'objectiu d'aquest quart sprint és la **securització de l'entorn Extagram** (P0.2). El focus se centra a blindar l'arquitectura actual implementant autenticació restringida amb Google (OAuth2), desplegant un Web Application Firewall (WAF), aïllant les xarxes internes de Docker i aplicant tècniques de hardening tant al sistema operatiu com a la base de dades.

---

## SPRINT BACKLOG (TASQUES PLANIFICADES)

### 1. Autenticació i WAF (Perímetre)

**Autenticació Google i Domini (@itb.cat)** Generació de credencials a Google Cloud, integració del contenidor oauth2-proxy i configuració de Nginx (auth_request) per restringir l'accés només a usuaris del domini de l'institut.  
Responsables: Adrián i Marc.  
Data límit: 20 de febrer.

**Implementació WAF (ModSecurity)** Substitució de la imatge base de l'S1 (proxy invers) per una imatge oficial amb regles OWASP CRS integrades per bloquejar atacs web.  
Responsable: Javier.  
Data límit: 20 de febrer.

### 2. Aïllament de Xarxa i Hardening (Infraestructura)

**Segmentació de Xarxes (Firewall)** Creació de xarxes aïllades al Docker Compose (frontend_net, backend_net, db_net) i configuració del tallafocs UFW a la màquina host.  
Responsables: Javier i Adrián.  
Data límit: 22 de febrer.

**Hardening OS i BBDD** Aplicació de sistemes d'arxius de només lectura (read_only: true), configuració d'usuaris no-root als contenidors, eliminació de l'exposició de ports de MySQL i ús de variables d'entorn segures.  
Responsables: Marc i Adrián.  
Data límit: 23 de febrer.

### 3. Qualitat, Gestió i Documentació

**Proves de Seguretat i Monitoratge de Tasques** Registre d'aquestes tasques a Proofhub (amb captura d'evidència), validació del trànsit al WAF (test de pujada d'imatges) i proves d'accés no autoritzat.  
Responsables: Adrián, Marc i Javier.  
Data límit: 24 de febrer.

---

## ASSIGNACIÓ DE RESPONSABILITATS

| Membre | Focus Principal al Sprint |
| :--- | :--- |
| **Javier** | **Xarxes i WAF:** Definició de l'aïllament de xarxes al Docker Compose i desplegament tècnic del Web Application Firewall a S1. |
| **Adrián** | **Seguretat Externa i BBDD:** Creació de credencials de Google, tallafocs UFW al host, hardening de la base de dades i actualització de Proofhub. |
| **Marc** | **Integració OAuth i Hardening OS:** Configuració de Nginx amb oauth2-proxy i aplicació del mode de només lectura als contenidors. |

---

## PLANIFICACIÓ TEMPORAL

| Fita | Data |
| :--- | :--- |
| **Inici del Sprint** | 17/02/2026 |
| **Integració WAF i Auth Google** | 20/02/2026 |
| **Finalització Aïllament i Hardening** | 23/02/2026 |
| **Fi del Sprint (Proves i Lliurament)** | 24/02/2026 |

**Riscos i Dependències:**
El risc principal és que les regles estrictes del WAF (ModSecurity) bloquegin falsos positius, com la pujada legítima d'imatges a l'script upload.php. Un altre risc és que l'activació del mode read_only trenqui l'execució de Nginx/PHP si no es configuren correctament els volums temporals en memòria RAM (tmpfs).

---

## CONCLUSIÓ

Aquest és un sprint de dues setmanes enfocat a l'enduriment de la infraestructura. No s'altera el codi font PHP de l'aplicació Extagram, sinó que es construeix una "cuirassa" de seguretat al voltant dels serveis existents utilitzant eines estàndard de la indústria.

---

**Acta redactada:** 17/02/2026 – 15:45 CET  
**Aprovat per:** Equip Extagram G2


[Torna a l'Inici](../../README.md)