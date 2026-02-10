# ACTA DE REUNIÓ - SPRINT PLANNING (SPRINT 3)

**Projecte:** Extagram G2  
**Data:** 3 de febrer de 2026  
**Hora:** 15:00 h 

---

## ASSISTENTS

Javier  
Adrián  
Marc  

---

## OBJECTIU DEL SPRINT

L'objectiu d'aquest tercer sprint és la **consolidació i estabilització del sistema**. Després del desplegament de Docker, el focus es desplaça a solucionar els problemes de comunicació interna entre contenidors (Xarxa), realitzar una bateria de proves exhaustiva (Testing) i tancar la documentació tècnica del projecte.

---

## SPRINT BACKLOG (TASQUES PLANIFICADES)

### 1. Optimització de Xarxa (Networking)

**Esquema arquitectura de red** Definició teòrica i visual de la topologia de xarxa ideal per als contenidors (subxarxes, aïllament de la BD).  
*Responsable:* Javier.  
*Data límit:* 3 de febrer.

**Arreglar problemas de red** Implementació tècnica de les correccions de xarxa. Resolució de conflictes de connectivitat interna o visibilitat entre serveis detectats durant el desplegament.  
*Responsable:* Adrián.  
*Data límit:* 10 de febrer.

### 2. Qualitat i Validació

**S9: Testing y Validación** Execució de proves end-to-end per assegurar que tots els contenidors (Web, BD, Uploads) funcionen coordinats i sense errors sota la nova arquitectura de xarxa.  
*Responsables:* Adrián, Marc i Javier.  
*Data límit:* 9 de febrer.

### 3. Tancament de Projecte

**S10: Documentación** Redacció de la memòria tècnica, guies de desplegament i actualització de l'acta final. Inclou documentar la configuració del Docker Compose i l'estructura de xarxa definitiva.  
*Responsables:* Adrián, Marc i Javier.  
*Data límit:* 10 de febrer.

---

## ASSIGNACIÓ DE RESPONSABILITATS

| Membre | Focus Principal al Sprint |
| :--- | :--- |
| **Javier** | **Disseny de Xarxa:** Definició de l'esquema de seguretat de xarxa i suport en la validació global. |
| **Adrián** | **Implementació de Xarxa:** Resolució tècnica dels errors de connexió (Troubleshooting) i configuració final de les xarxes Docker. |
| **Marc** | **QA i Documentació:** Focus principal en el testing funcional de l'aplicació i redacció de la documentació final. |

---

## PLANIFICACIÓ TEMPORAL

| Fita | Data |
| :--- | :--- |
| **Inici del Sprint** | 03/02/2026 |
| **Definició Esquema Xarxa** | 03/02/2026 |
| **Finalització Testing** | 09/02/2026 |
| **Fi del Sprint (Lliurament Final)** | 10/02/2026 |

**Riscos i Dependències:**
El risc principal és que els ajustos a la xarxa interna ("Arreglar problemas de red") trenquin temporalment la connectivitat entre el servidor web i la base de dades, bloquejant les proves de validació (Testing) fins que es resolgui.

---

## CONCLUSIÓ

Aquest és un sprint curt i intensiu (1 setmana) enfocat a la qualitat. No s'afegeixen noves funcionalitats ("features"), sinó que es prioritza que l'arquitectura sigui robusta, segura (a nivell de xarxa) i estigui ben documentada per al lliurament final.

---

**Acta redactada:** 03/02/2026 – 15:15 CET  
**Aprovat per:** Equip Extagram G2