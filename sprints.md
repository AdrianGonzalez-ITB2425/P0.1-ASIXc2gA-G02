# Metodologia i Seguiment del Projecte

El desenvolupament d'**Extagram G2** s'ha regit per la metodologia **Agile Scrum**. Aquest marc de treball ens ha permès abordar la complexitat de la infraestructura de manera incremental, dividint el projecte en blocs de treball manejables (Sprints) i adaptant-nos als requisits tècnics a mesura que avançàvem.

Hem estructurat el cicle de vida en **3 Sprints quinzenals**, amb rols definits (Scrum Master, Development Team) i cerimònies d'inici (Planning) i tancament (Review) per assegurar l'assoliment dels objectius.

---

## Índex de Sprints

A continuació es detalla la documentació oficial generada durant cada fase del projecte, accessible directament:

### Sprint 1: Desplegament Inicial (MVP)
En aquesta primera fase, l'objectiu va ser establir la infraestructura base a AWS i desplegar l'aplicació en una arquitectura monolítica funcional.
* [ Sprint Planning 1](./Sprints/Sprint1/sprint_planning1.md) – Definició de l'stack tecnològic i rols.
* [ Sprint Review 1](./Sprints/Sprint1/sprint_review1.md) – Validació del funcionament en instància única.

### Sprint 2: Dockerització i Microserveis
La fase més crítica, on es va migrar l'aplicació a contenidors, implementant el Proxy Invers i segregant serveis.
* [ Sprint Planning 2](./Sprints/Sprint2/sprint_planning2.md) – Disseny de l'orquestració amb Docker Compose.
* [ Sprint Review 2](./Sprints/Sprint2/sprint_review2.md) – Desplegament de contenidors i domini HTTPS.

### Sprint 3: Xarxa i Consolidació
Fase final centrada en la seguretat de la xarxa interna (networking), proves de càrrega i documentació tècnica.
* [ Sprint Planning 3](./Sprints/Sprint3/sprint_planning3.md) – Planificació de la topologia de xarxa i testing.
* [ Sprint Review 3](./Sprints/Sprint3/sprint_review3.md) – Tancament del projecte i validació final.

---

## Eines de Gestió
Per al seguiment de les tasques i el control del backlog, hem utilitzat un tauler Kanban digital (ProofHub), permetent-nos visualitzar l'estat de cada funcionalitat (To Do, Doing, Review, Done) en temps real.


[Torna a l'Inici](./README.md)