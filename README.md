# PROJECTE 0.1 - Desplegament d’Extagram
**Elaborat per:** *Adrián González, Marc Manzorro, Javier Vericat*  
**Data:** Desembre 2025/26  

## Índex proposat
1. Introducció
2. Estudi previ
3. Sprints del projecte
4. Estructura de l'arquitectura
5. Disseny de xarxa

## Descripció del projecte

Aquest projecte consisteix en el desplegament d’una aplicació web anomenada **Extagram**, una aplicació semblant a una xarxa social que permet als usuaris publicar textos i pujar imatges [file:1].

L’aplicació està desenvolupada en **PHP** i utilitza una base de dades **MySQL** per emmagatzemar les publicacions i la informació de les imatges [file:1]. L’objectiu és implementar una arquitectura d’alta disponibilitat i escalabilitat, on diversos serveis (NGINX, PHP-FPM, MySQL) estan segregats en diferents contenidors Docker i interconnectats a través d’una xarxa, podent escollir el desplegament en un entorn de núvol [file:1].

## Objectiu principal

Fer el desplegament de l’aplicació Extagram amb alta disponibilitat i escalabilitat, seguint una arquitectura de microserveis amb Docker, on:

- El trànsit web entra per un **proxy invers NGINX** (S1) que fa balanceig de càrrega entre els servidors PHP-FPM que gestionen la part dinàmica principal (S2 i S3) [file:1].
- Un servidor PHP-FPM dedicat (S4) gestiona la càrrega d’imatges (`upload.php`) i les emmagatzema en un directori o en base de dades [file:1].
- Els fitxers estàtics (imatges, CSS, SVG) es serveixen des de servidors NGINX separats (S5 per imatges, S6 per CSS/SVG) [file:1].
- La base de dades MySQL (S7) emmagatzema les publicacions i replica el contingut de la carpeta d’imatges per garantir disponibilitat encara que el servidor d’imatges falli [file:1].

En un segon projecte (P0.2) s’afegiran capes de seguretat i automatització al desplegament [file:1].

## Estructura de l’arquitectura

L’arquitectura proposada consta dels següents elements:

- **S1 (nginx:alpine)**: Proxy invers NGINX que rep totes les peticions del navegador i fa balanceig de càrrega per a `extagram.php` entre S2 i S3 [file:1].
- **S2 i S3 (php:fpm)**: Servidors PHP-FPM que executen `extagram.php` (part dinàmica principal de la web) [file:1].
- **S4 (php:fpm)**: Servidor PHP-FPM que executa `upload.php` i emmagatzema les imatges en un directori local o en base de dades [file:1].
- **S5 (nginx:alpine)**: Servidor NGINX que serveix les imatges pujades a través de S4 (part estàtica: imatges) [file:1].
- **S6 (nginx:alpine)**: Servidor NGINX que serveix els fitxers estàtics `style.css` i `preview.svg` (part estàtica: CSS/SVG) [file:1].
- **S7 (mysql)**: Base de dades MySQL que conté la taula `posts` i replica el contingut de la carpeta d’imatges com a blobs en cas de fallada [file:1].

## Sprints del projecte

El projecte es divideix en tres sprints quinzenals de 10 hores de duració cadascun:

- **Sprint 1 (15/12/2025 - 19/01/2026)**: Anàlisi del projecte, tecnologies i disseny mínimament viable (MVP) en una sola màquina amb NGINX/Apache i PHP, capaç de carregar imatges via base de dades o carpeta [file:1].
- **Sprint 2 (19/01/2026 - 27/01/2026)**: Segregació de l’aplicació en contenidors Docker locals, amb xarxa pont, proxy invers, balanceig i segregació de peticions [file:1].
- **Sprint 3 (02/02/2026 - 10/02/2026)**: Finalització de l’arquitectura Docker, disseny de xarxa (amb Packet Tracer o similar), proves d’operativitat i caiguda de nodes redundants [file:1].



Aquesta és la proposta d’índex per a la documentació del projecte en el repositori GitHub:

