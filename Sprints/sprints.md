+# Metodologia i Seguiment del Projecte

El desenvolupament d'**Extagram G2** s'ha regit per la metodologia **Agile Scrum**. Aquest marc de treball ens ha permès abordar la complexitat de la infraestructura de manera incremental, dividint el projecte en blocs de treball manejables (Sprints) i adaptant-nos als requisits tècnics a mesura que avançàvem.

Hem estructurat el cicle de vida en **5 Sprints quinzenals**, amb rols definits (Scrum Master, Development Team) i cerimònies d'inici (Planning) i tancament (Review) per assegurar l'assoliment dels objectius.

---

## Índex de Sprints

A continuació es detalla la documentació oficial generada durant cada fase del projecte, accessible directament:

### Sprint 1: Desplegament Inicial (MVP)
En aquesta primera fase, l'objectiu va ser establir la infraestructura base a AWS i desplegar l'aplicació en una arquitectura monolítica funcional.
[ Sprint Planning 1](./Sprint1/sprint_planning1.md) – Definició de l'stack tecnològic i rols.
[ Sprint Review 1](./Sprint1/sprint_review1.md) – Validació del funcionament en instància única.

### Sprint 2: Dockerització i Microserveis
La fase més crítica, on es va migrar l'aplicació a contenidors, implementant el Proxy Invers i segregant serveis.
[ Sprint Planning 2](./Sprint2/sprint_planning2.md) – Disseny de l'orquestració amb Docker Compose.
[ Sprint Review 2](./Sprint2/sprint_review2.md) – Desplegament de contenidors i domini HTTPS.

### Sprint 3: Xarxa i Consolidació
Fase centrada en la seguretat de la xarxa interna (networking), proves de càrrega i documentació tècnica.
[ Sprint Planning 3](./Sprint3/sprint_planning3.md) – Planificació de la topologia de xarxa i testing.
[ Sprint Review 3](./Sprint3/sprint_review3.md) – Consolidació del projecte i validació de xarxes.

### Sprint 4: Seguretat i Enduriment
Fase centrada en la securització de l'arquitectura mitjançant la segmentació de xarxes internes, implementació d'un WAF i tècniques de hardening al sistema i base de dades.
[ Sprint Planning 4](./Sprint4/sprint_planning4.md) – Planificació de polítiques de seguretat i aïllament.
[ Sprint Review 4](./Sprint4/sprint_review4.md) – Validació del WAF, hardening aplicat i resolució d'errors web.

### Sprint 5: Monitoratge i Tancament
Última fase dedicada a l'observabilitat del sistema amb Elasticsearch, disseny de backups automatitzats (AWS Glacier) i preparació per a la presentació final.
[ Sprint Planning 5](./Sprint5/sprint_planning5.md) – Definició de la integració d'Elasticsearch i l'estratègia de retenció de dades.
[ Sprint Review 5](./Sprint5/sprint_review5.md) – Validació d'Elasticsearch, documentació de limitacions a AWS i tancament global del projecte.

---

## Eines de Gestió
Per al seguiment de les tasques i el control del backlog, hem utilitzat un tauler Kanban digital (ProofHub), permetent-nos visualitzar l'estat de cada funcionalitat (To Do, Doing, Review, Done) en temps real.


[Torna a l'Inici](../README.md)