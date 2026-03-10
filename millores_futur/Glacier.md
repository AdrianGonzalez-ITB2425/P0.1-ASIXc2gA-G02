# Backup Automàtic a AWS S3 Glacier

## La idea: Backup automàtic a S3 Glacier

### Descripció de la solució dissenyada

La solució plantejada consistia a crear un sistema de backup completament automatitzat que realitzés còpies de seguretat periòdiques de tots els components crítics de l'aplicació i les emmagatzemés a **AWS S3 amb classe d'emmagatzematge Glacier**, pensada per a arxivat a llarg termini amb un cost molt reduït.

El sistema s'havia de compondre de tres elements:

- Un **script Bash** (`backup-extagram.sh`) que realitzés totes les operacions de còpia: aturada controlada dels contenidors, exportació de la base de dades amb `mysqldump`, còpia de la carpeta d'uploads, còpia del `docker-compose.yml` i empaquetat en un fitxer `.tar.gz` amb data i hora.
- Un **servei systemd** (`extagram-backup.service`) que executés l'script de forma controlada i registrés el resultat al journal del sistema.
- Un **temporitzador systemd** (`extagram-backup.timer`) que llancés el servei automàticament els dies i hores programats, sense necessitat d'intervenció humana.

### Per què S3 Glacier

AWS S3 Glacier és un servei d'emmagatzematge al núvol dissenyat específicament per a arxivat i còpies de seguretat a llarg termini. El seu cost per gigabyte és significativament inferior al d'S3 estàndard, cosa que el fa ideal per a backups que no necessiten ser recuperats de forma immediata però que han d'estar disponibles en cas d'emergència.

A més, en tractar-se d'un servei completament gestionat per AWS, les dades queden emmagatzemades de forma redundant i durada fora de la instància EC2, de manera que una fallada de la instància no afectaria les còpies de seguretat.

### Estat d'implementació assolit

El sistema va arribar a implementar-se gairebé per complet:

- L'script de backup funcionava correctament: aturava els contenidors, exportava la base de dades amb `mysqldump`, copiava els fitxers d'uploads i el `docker-compose.yml`, i empaquetava tot en un fitxer `.tar.gz` amb marca de temps. ✅
- El servei i el temporitzador systemd estaven configurats, habilitats i actius, programats per executar-se automàticament. ✅
- El bucket S3 `extagram-backups` estava creat a la regió `us-east-1`. ✅
- Únicament fallava el pas final: la pujada del fitxer a S3, per manca d'autenticació vàlida amb AWS. ❌

## El problema: limitacions d'AWS Academy

### Credencials temporals sense solució permanent

L'entorn utilitzat per al projecte és **AWS Academy Learner Lab**, una plataforma educativa que proporciona accés limitat als serveis d'AWS per a pràctiques formatives. Aquesta plataforma imposa restriccions importants que van bloquejar la implementació del backup automàtic:

- **No es poden crear usuaris IAM permanents.** La consola IAM no permet crear usuaris nous ni generar Access Keys de llarga durada, que són les credencials necessàries per autenticar l'AWS CLI en un servidor.
- **Les credencials disponibles són temporals.** AWS Academy genera automàticament credencials amb `aws_access_key_id`, `aws_secret_access_key` i `aws_session_token` que **caduquen al cap de poques hores**, quan es tanca la sessió del Learner Lab.
- **No es pot assignar un IAM Role a la instància EC2.** En un entorn AWS real, la solució òptima seria adjuntar un IAM Role directament a la instància EC2 perquè obtingui credencials automàticament, sense necessitat de configurar-les manualment. A AWS Academy, aquesta opció tampoc no estava disponible.

## Valoració de la idea

Malgrat no haver pogut completar la implementació, la solució dissenyada és **tècnicament sòlida i molt adequada** per a un entorn de producció real. En un context AWS fora de la plataforma educativa, el sistema funcionaria de la manera següent:

1. Crear un usuari IAM amb permisos exclusivament sobre el bucket S3 (`s3:PutObject`, `s3:ListBucket`).
2. Generar una Access Key permanent per a aquest usuari i configurar-la al servidor amb `aws configure`.
3. Alternativament, adjuntar un IAM Role a la instància EC2 amb els mateixos permisos, eliminant la necessitat de gestionar claus manualment.
4. El temporitzador systemd s'encarregaria de tot automàticament, sense cap intervenció humana.

El cost de la solució en producció real seria molt baix: els backups comprimits d'una aplicació com Extagram ocupen pocs megabytes, i la classe d'emmagatzematge Glacier té un cost per gigabyte inferior a 0,004 USD al mes.

## Resum de l'estat final

| Component | Estat |
|---|---|
| Script de backup (`backup-extagram.sh`) | ✅ Implementat i funcional |
| Servei systemd (`extagram-backup.service`) | ✅ Configurat i habilitat |
| Temporitzador systemd (`extagram-backup.timer`) | ✅ Actiu i programat |
| Bucket S3 `extagram-backups` | ✅ Creat a `us-east-1` |
| AWS CLI instal·lat | ⚠️ Identificat com a necessari |
| Autenticació AWS permanent | ❌ Bloquejat per limitacions d'AWS Academy |
| Pujada automàtica a S3 Glacier | ❌ No assolida |

La tasca queda documentada i **pendent de completar en un entorn AWS real** on sigui possible generar credencials IAM permanents o adjuntar un IAM Role a la instància.

---

[Torna a l'Inici](./README.md)