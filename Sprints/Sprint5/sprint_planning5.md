# ACTA DE REUNIÓ - SPRINT PLANNING (SPRINT 5)

**Projecte:** Extagram G2  
**Data:** 03 de març de 2026  
**Hora:** 15:00 h 

---

## ASSISTENTS

- Adrián González
- Javier Vericat
- Marc Manzorro

---

## OBJECTIU DEL SPRINT

L'objectiu d'aquest cinquè sprint és la **implementació del sistema de monitoratge i còpies de seguretat** (P0.3). El focus se centra a desplegar Elasticsearch per a la gestió centralitzada de logs, configurar una web de control visual del rendiment dels serveis, i implementar còpies de seguretat automatitzades a AWS Glacier per garantir la recuperació de dades en cas de desastre.

---

## SPRINT BACKLOG (TASQUES PLANIFICADES)

### 1. Elasticsearch i Gestió de Logs (Monitoratge)

**Implementació Elasticsearch** Desplegament del contenidor Elasticsearch amb Docker Compose, configuració dels índexs per a logs d'aplicació i integració amb els serveis existents (Nginx, PHP, MySQL).  
**Responsable:** Adrián.  
**Data límit:** 06 de març.

**Documentació Elasticsearch** Creació de la documentació tècnica sobre la configuració d'Elasticsearch, consultes bàsiques i manteniment dels índexs de logs.  
**Responsables:** Adrián i Marc.  
**Data límit:** 07 de març.

### 2. Web de Control de Logs i Rendiment (Visualització)

**Dashboard de Logs i Mètriques** Desenvolupament d'una interfície web per visualitzar els logs de la pàgina de manera accessible i monitorar el rendiment dels servidors en temps real.  
>  **Nota:** Aquesta tasca està actualment **BLOQUEJADA** per dependència de la finalització d'Elasticsearch.  
**Responsable:** Marc.  
**Data límit:** 10 de març.

**Integració Kibana/OUI** Configuració de Kibana com a capa de visualització sobre Elasticsearch per crear dashboards personalitzats de mètriques de rendiment.  
**Responsables:** Marc i Javier.  
**Data límit:** 10 de març.

### 3. Backups i Recuperació (Continuïtat)

**Backups AWS Glacier** Configuració del sistema de còpies de seguretat automatitzades cap a AWS Glacier Deep Archive per a emmagatzematge de baix cost i llarga durada.  
**Responsables:** Adrián i Marc.  
**Data límit:** 09 de març.

**Polítiques de Retenció** Definició de les polítiques de cicle de vida dels backups (retenció 90 dies a Glacier, 30 dies en calent) i procediments de recuperació.  
**Responsables:** Marc i Adrián.  
**Data límit:** 09 de març.

### 4. Qualitat, Gestió i Documentació

**Proves de Recuperació i Validació** Execució de proves de restauració de backups, validació de la integritat de les dades i registre d'evidències a Proofhub.  
**Responsables:** Adrián, Marc i Javier.  
**Data límit:** 11 de març.

**Documentació i Gestió del Sprint** Actualització de la wiki del projecte, registre de tasques i preparació de la demostració per al sprint review.  
**Responsables:** Tot l'equip.  
**Data límit:** 11 de març.

---

## ASSIGNACIÓ DE RESPONSABILITATS

| Membre | Focus Principal al Sprint |
| :--- | :--- |
| **Adrián** | **Infraestructura i Backups:** Configuració de les polítiques de retenció a AWS Glacier, validació de procediments de recuperació i suport en la integració de Kibana. |
| **Adrián** | **Elasticsearch i Coordinació:** Desplegament tècnic d'Elasticsearch, documentació del sistema de logs, coordinació de backups i actualització de Proofhub. |
| **Marc** | **Visualització i Dashboard:** Desenvolupament de la interfície web de control de logs, integració de Kibana/OUI i resolució de la tasca bloquejada. |

---

## PLANIFICACIÓ TEMPORAL

| Fita | Data |
| :--- | :--- |
| **Inici del Sprint** | 03/03/2026 |
| **Elasticsearch Operatiu** | 06/03/2026 |
| **Backups Glacier Configurats** | 09/03/2026 |
| **Dashboard de Logs Completat** | 10/03/2026 |
| **Fi del Sprint (Proves i Lliurament)** | 11/03/2026 |

---

## RISCOS I DEPENDÈNCIES

| Risc | Impacte | Mitigació |
| :--- | :--- | :--- |
| **Tasca bloquejada (Dashboard)** | Alt | Prioritzar la finalització d'Elasticsearch per desbloquejar la web de logs. |
| **Costos AWS Glacier imprevistos** | Mig | Configurar alertes de facturació i revisar polítiques de retenció setmanalment. |
| **Complexitat consultes Elasticsearch** | Mig | Utilitzar plantilles predefinides i documentar consultes comunes des del dia 1. |
| **Volum de logs excessiu** | Mig | Implementar rotació de logs i filtratge per nivell d'importància (ERROR, WARN). |

---

## CONCLUSIÓ

Aquest és un sprint de dues setmanes enfocat a la **observabilitat i resiliència** del sistema Extagram. Després de la securització del Sprint 4, ara es construeixen les capacitats de monitoratge i recuperació que permetran detectar incidents ràpidament i garantir la continuïtat del servei. La implementació d'Elasticsearch proporcionarà visibilitat completa del sistema, mentre que AWS Glacier assegurarà que les dades estiguin protegides a llarg termini amb un cost optimitzat.

### Punts d'Atenció Especial:

-  La tasca de **"WEB, CONTROL DE LOGS I RENDIMENT"** està actualment **BLOQUEJADA** i requereix atenció prioritària.
-  El volum de comentaris a la tasca d'Elasticsearch (**6 comentaris**) indica activitat intensa; cal revisar si hi ha impediments tècnics.
-  Les còpies de Glacier han de provar-se amb una **restauració real** abans de donar per tancada la tasca.

---

**Acta redactada:** 03/03/2026 – 15:45 CET  
**Aprovat per:** Equip Extagram G2

---

[Torna a l'Inici](../../README.md)