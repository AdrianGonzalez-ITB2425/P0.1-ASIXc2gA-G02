# Configuració de Domini i Seguretat HTTPS

Per tal de desplegar **Extagram** en un entorn accessible des de l'exterior de forma segura, s'ha configurat un nom de domini dinàmic i s'ha assegurat la connexió mitjançant xifratge SSL/TLS.

A continuació es detalla la infraestructura de xarxa pública i la gestió de certificats digitals.

---

## 1. Resolució de Noms (DNS Dinàmic)

Atès que el servidor de desplegament es troba en una xarxa domèstica amb **IP Pública Dinàmica**, s'ha utilitzat el servei **DuckDNS** per mantenir l'accessibilitat constant als serveis.

### Configuració del Domini
* **Proveïdor:** DuckDNS.
* **Domini Assignat:** `extagram-g2.duckdns.org`.
* **Mecanisme:** El servei associa aquest nom de domini a l'IP pública actual del router, permetent l'accés extern sense necessitat de conèixer l'adreça numèrica, que pot variar en reiniciar l'encaminador.

### Automatització de l'Actualització (Scripting)
Per evitar la pèrdua de connexió quan el proveïdor d'internet canvia l'IP pública, s'ha implementat un mecanisme de sincronització automàtica al *host*.

1.  **Script de Sincronització (`duckdns_update.sh`):**
    S'ha creat un script en *bash* que realitza una petició `curl` a l'API de DuckDNS enviant el **Token d'autenticació** únic del projecte. Això notifica al servidor DNS quina és la nostra IP actual.

    ```bash
    #!/bin/bash
    # Petició a l'API de DuckDNS per actualitzar la IP
    echo url="[https://www.duckdns.org/update?domains=extagram-g2&token=EL_TEU_TOKEN_PRIVAT&ip=](https://www.duckdns.org/update?domains=extagram-g2&token=EL_TEU_TOKEN_PRIVAT&ip=)" | curl -k -o /var/log/duckdns.log -K -
    ```

2.  **Persistència (Cron):**
    S'ha configurat el programador de tasques del sistema (`crontab`) per executar aquest script cada 5 minuts. Això garanteix que l'IP estigui sincronitzada gairebé en temps real davant de qualsevol canvi.

    ```bash
    # Configuració al crontab del host
    */5 * * * * ~/scripts/duckdns_update.sh >/dev/null 2>&1
    ```

---

## 2. Xifratge SSL/TLS (Let's Encrypt)

Per complir amb els estàndards de seguretat actuals i protegir les credencials dels usuaris, s'ha implementat HTTPS utilitzant **Let's Encrypt**.

### Generació de Certificats
S'ha utilitzat l'eina **Certbot** al sistema amfitrió (*host*). El procés d'obtenció de certificats s'ha realitzat mitjançant la validació `standalone`.

* **Estratègia:** Com que el servidor web (Nginx) resideix dins d'un contenidor Docker, es va aturar temporalment el servei `s1-proxy` per alliberar el port 80 del *host* i permetre que Certbot realitzés la validació del domini.
* **Comanda d'Execució:**
    ```bash
    sudo certbot certonly --standalone -d extagram-g2.duckdns.org
    ```
* **Resultat:** Es van generar dos fitxers clau al directori `/etc/letsencrypt/live/extagram-g2.duckdns.org/`:
    * `fullchain.pem`: El certificat públic complet (cadena de confiança).
    * `privkey.pem`: La clau privada del servidor.

---

## 3. Integració amb Docker (Proxy Invers)

Un cop obtinguts els certificats al sistema operatiu amfitrió, aquests s'injecten al contenidor `s1-proxy` (Nginx) mitjançant volums per habilitar la terminació SSL.

### Configuració de Volums (`docker-compose.yml`)
El servei `s1-proxy` munta el directori de certificats del *host* en mode lectura (`:ro`). Això permet que Nginx accedeixi a les claus generades per Certbot sense haver d'instal·lar Certbot dins del contenidor.

```yaml
  s1-proxy:
    image: nginx:alpine
    ports:
      - "80:80"   # Redirecció HTTP -> HTTPS
      - "443:443" # Trànsit HTTPS segur
    volumes:
      - ./s1-proxy/s1.conf:/etc/nginx/conf.d/default.conf:ro
      # Mapeig dels certificats del Host al Contenidor
      - /etc/letsencrypt:/etc/letsencrypt:ro

```
[Torna a l'Inici](../README.md)