## Context del projecte

L’activitat P0.1 demana desplegar una aplicació en PHP que permet pujar imatges i mostrar-les, amb una arquitectura dividida en diversos serveis: proxy invers i balanceig (S1), servidors PHP-FPM per a la part dinàmica (S2 i S3), servidor d’upload (S4), servidor d’imatges (S5), servidor d’estàtics (S6) i base de dades MySQL (S7). [file:1]  
A més, es defineixen tres sprints: el primer per fer anàlisi i disposar d’un servidor web funcional en una sola màquina, i els sprints 2 i 3 per separar l’aplicació mitjançant Docker, connectant els contenidors en xarxa pont i incorporant el proxy invers i el balanceig de càrrega. [file:1]

Aquest plantejament obliga a:

- Treballar amb un servidor Linux real al núvol.  
- Practicar instal·lació i configuració de serveis (Nginx/Apache, PHP-FPM, MySQL).  
- Evolucionar cap a una arquitectura de múltiples contenidors que simulin alta disponibilitat.

## Per què AWS en comptes d’Azure o altres núvols

### Ecosistema, ús real i documentació

AWS és una de les plataformes de núvol públic més implantades per a aplicacions web i empresarials, especialment en entorns Linux amb Nginx/Apache, PHP i MySQL. [web:1]  
Aquesta popularitat fa que hi hagi una gran quantitat de guies, tutorials i exemples pràctics exactament sobre el tipus d’arquitectura que volem implementar, cosa que facilita l’autoaprenentatge i la resolució de problemes a segon d’ASIX.

Azure també ofereix màquines virtuals Linux, però històricament ha estat més orientat a integració amb serveis Microsoft (Windows Server, Active Directory, SQL Server), que no són els protagonistes del nostre projecte, basat en PHP, Nginx i MySQL.  
Altres proveïdors més petits poden ser vàlids, però sovint tenen menys documentació específica i exemples educatius, fet que pot complicar la feina quan apareixen errors de configuració o dubtes de xarxa.

### Adequat per a servidors Linux i LEMP

AWS proporciona imatges oficials d’Ubuntu i Amazon Linux optimitzades per a muntar servidors web, amb suport directe per instal·lar paquets com Nginx, Apache, PHP-FPM i MySQL o MariaDB. [web:2]  
Això encaixa perfectament amb el requeriment del projecte de repassar la instal·lació i configuració de mòduls i serveis en un servidor, ja que permet seguir el mateix esquema que tindríem en un servidor físic, però al núvol. [file:1]

### Control del cost amb un pressupost d’uns 50 €

Amb AWS es poden seleccionar instàncies petites (per exemple, tipus t2.micro o similars) amb un cost mensual reduït i que, si s’utilitzen poques hores, poden mantenir-se per sota d’uns pocs euros al mes. [web:2]  
Tenint un pressupost màxim aproximat de 50 €, és viable:

- Tenir una instància EC2 petita activa durant les setmanes del projecte.  
- Aturar la instància quan no s’està treballant per reduir encara més el cost.  

Altres serveis més gestionats i abstractes poden tenir models de preus basats en peticions, tràfic o recursos automàticament escalats que són més difícils de controlar, i podrien fer més complicat assegurar-se de no superar el límit econòmic.

### Coherència amb els objectius del projecte

El projecte remarca la importància de la gestió, la documentació i la planificació, i exigeix que la infraestructura sigui configurable i entenedora per tot l’equip. [file:1]  
AWS permet treballar amb una arquitectura clara sobre EC2, on el grup pot documentar cada pas (creació de la instància, configuració dels serveis, proves) i defensar-lo davant el professorat amb exemples concrets i fàcilment reproduïbles.

## Per què començar amb EC2 abans que centrar-se només en Docker

### Respectar la seqüència didàctica dels sprints

Segons el document, el **Sprint 1** ha de produir un primer disseny mínimament viable amb un servidor web funcional en una sola màquina (NGINX o Apache), capaç de carregar imatges via base de dades o upload a carpeta. [file:1]  
Només als **Sprints 2 i 3** es demana segregar l’aplicació mitjançant Dockers, simulant els servidors S1–S7 en una xarxa de contenidors amb proxy invers i balanceig, però encara sense tractar aspectes avançats de seguretat o caiguda real de la base de dades. [file:1]

Utilitzar una instància EC2 com a base s’ajusta exactament a aquesta planificació:

- Primer, es munta el servidor “clàssic” (LEMP/LAMP) en una sola EC2.  
- Després, sobre la mateixa EC2, s’afegeix Docker i s’hi despleguen els diferents serveis S1–S7.

### Aprendre l’administració de sistemes “real”

En una EC2 tenim accés complet al sistema operatiu: podem gestionar paquets, usuaris, permisos, firewall, serveis i fitxers de configuració, igual que en un servidor físic. [web:2]  
Això permet:

- Entendre com es configura Nginx o Apache per servir PHP i estàtics.  
- Veure com es configura MySQL (usuaris, permisos, fitxers de dades).  
- Analitzar logs d’accés, d’errors i de servei per diagnosticar problemes.

Aquest aprenentatge és central en un cicle com ASIX, on es valora que l’alumnat entengui què passa a cada capa de la infraestructura, més enllà de prémer botons en una consola web.

### Docker com a capa superior, no com a substitut d’EC2

El projecte indica que, als Sprints 2 i 3, tot s’ha de segregar mitjançant Docker, amb un proxy invers Nginx, contenidors PHP-FPM per extagram.php i upload.php, un servidor d’imatges, un servidor d’estàtics i un contenidor MySQL. [file:1]  
Treballar sobre una EC2 permet:

- Comparar directament el comportament del monòlit (tot en una màquina) amb la versió distribuïda en contenidors.  
- Veure com els serveis que abans eren processos del sistema passen a ser contenidors independents amb les seves pròpies configuracions i volums.  
- Gestionar la xarxa Docker (xarxa pont) que simula la interconnexió entre S1–S7.

D’aquesta manera, Docker esdevé una eina per modularitzar i replicar serveis, però no amaga la infraestructura de base, sinó que s’hi construeix a sobre.

### Complexitat adequada i control de recursos

Plataformes exclusivament orientades a contenidors introdueixen conceptes com clusters, rols de control, autoscaling, plantilles complexes i serveis gestionats de xarxa. [web:2]  
Per a un projecte com Extagram, amb un únic stack i un temps limitat per sprint:

- Una sola EC2 amb Docker i `docker-compose` és suficient per simular alta disponibilitat (dos contenidors S2/S3, balanceig a S1, etc.).  
- El consum de recursos es concentra en una instància, cosa que facilita controlar l’ús de CPU, memòria i disc dins del pressupost disponible.

A més, en cas de problema, es pot fer diagnòstic des d’un únic punt:

- Logs del sistema (journal, syslog).  
- Logs de Nginx, PHP-FPM i MySQL.  
- Logs i estat dels contenidors Docker.