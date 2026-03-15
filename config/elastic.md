# Documentació del Desplegament: Stack ELK (Elastic)

Aquest document detalla la configuració i l'arquitectura de l'stack ELK (Elasticsearch, Logstash i Kibana) desplegat mitjançant Docker Compose per al projecte **Extagram**. La versió utilitzada per a tots els components és la **8.11.0**.

## 🏗️ Resum de l'Arquitectura

El desplegament consta de tres serveis principals comunicats a través d'una xarxa interna de Docker, amb persistència de dades configurada per a Elasticsearch.

| Servei | Contenidor | Port Exposat | Funció Principal |
| :--- | :--- | :--- | :--- |
| **Elasticsearch** | `elk-elasticsearch` | `9200` | Motor de cerca i emmagatzematge de dades. |
| **Logstash** | `elk-logstash` | `5044` | Processament i ingesta de dades. |
| **Kibana** | `elk-kibana` | `5601` | Interfície gràfica i visualització de dades. |

---

## Detall dels Serveis

### 1. Elasticsearch (`elk-elasticsearch`)

Actua com el cor de l'stack, emmagatzemant i indexant els logs o dades ingerides.

* **Imatge:** `docker.elastic.co/elasticsearch/elasticsearch:8.11.0`
* **Mode de desplegament:** Node únic (`discovery.type=single-node`).
* **Seguretat:** X-Pack Security i SSL estan **desactivats** (`xpack.security.enabled=false`). *Nota: Recomanat només per a entorns de desenvolupament o xarxes segures.*
* **Memòria:** Configurat amb un límit de Heap d'1GB (`-Xms1g -Xmx1g`).
* **Persistència:** Utilitza el volum de Docker `es` muntat a `/usr/share/elasticsearch/data`.
* **Healthcheck:** Monitoritza l'estat de salut del clúster cada 30 segons utilitzant una comanda `curl`.

### 2. Logstash (`elk-logstash`)

Encarregat de rebre la informació, transformar-la i enviar-la a Elasticsearch.

* **Imatge:** `docker.elastic.co/logstash/logstash:8.11.0`
* **Dependència:** Espera que `elasticsearch` estigui iniciat.
* **Memòria:** Configurat amb un límit de Heap de 512MB (`-Xmx512m -Xms512m`).
* **Pipelines:** Munta els fitxers de configuració en mode només lectura des de la ruta local `./logstash/pipeline` cap a `/usr/share/logstash/pipeline`.

### 3. Kibana (`elk-kibana`)

Proporciona la interfície d'usuari per explorar les dades emmagatzemades a Elasticsearch.

* **Imatge:** `docker.elastic.co/kibana/kibana:8.11.0`
* **Dependència:** Espera que `elasticsearch` estigui iniciat.
* **Connexió:** Es comunica amb Elasticsearch a través de la xarxa interna utilitzant la URL `http://elasticsearch:9200`.
* **Seguretat i Xifratge:** Security està desactivat, però s'han definit claus estàtiques de xifratge (32 caràcters) per a sessions, informes i objectes desats.

---

## Xarxes i Volums

* **Xarxa (`elk-net`):** Xarxa de tipus *bridge* aïllada per a la comunicació interna entre els tres contenidors.
* **Volum (`es`):** Volum gestionat per Docker per garantir que els índexs d'Elasticsearch no es perdin si el contenidor es reinicia o es destrueix.