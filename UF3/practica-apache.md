# Pràctica d'instal·lació d'Apache i DNS

## Primera part: Instal·lació i configuració bàsica

### 1. Instal·lar Ubuntu i configurar la xarxa

1. Instal·lar Ubuntu Server a la màquina virtual.
2. Assignar una IP fixa a la màquina Ubuntu. Editar el fitxer de configuració de la xarxa:

   ```bash
   sudo nano /etc/netplan/00-installer-config.yaml
   ```

3. Configurar-lo amb els següents paràmetres:

   ```yaml
   network:
     ethernets:
       enp0s3:
         dhcp4: no
         addresses:
           - 10.0.2.5/24
         gateway4: 10.0.2.1
         nameservers:
           addresses:
             - 10.0.2.5
   version: 2
   ```

4. Aplicar la configuració:

   ```bash
   sudo netplan apply
   ```

### 2. Instal·lar i configurar el servidor DNS

1. Instal·lar el servidor DNS Bind9:

   ```bash
   sudo apt update
   sudo apt install bind9 -y
   ```

2. Editar el fitxer de configuració principal:

   ```bash
   sudo nano /etc/bind/named.conf.local
   ```

   Afegir les zones per al domini `nomalumne.local`:

   ```bash
   zone "nomalumne.local" {
       type master;
       file "/etc/bind/db.nomalumne.local";
   };
   ```

3. Crear el fitxer de configuració de la zona:

   ```bash
   sudo cp /etc/bind/db.local /etc/bind/db.nomalumne.local
   sudo nano /etc/bind/db.nomalumne.local
   ```

   Modificar-lo perquè contingui:

   ```bash
   ;
   ; BIND data file for nomalumne.local
   ;
   $TTL    604800
   @       IN      SOA     nomalumne.local. root.nomalumne.local. (
                         2         ; Serial
                         604800    ; Refresh
                         86400     ; Retry
                         2419200   ; Expire
                         604800 )  ; Negative Cache TTL
   ;
   @       IN      NS      nomalumne.local.
   @       IN      A       10.0.2.5
   www     IN      A       10.0.2.5
   ```

4. Reiniciar el servei DNS:

   ```bash
   sudo systemctl restart bind9
   ```

### 3. Instal·lar i configurar el servidor web Apache

1. Instal·lar Apache:

   ```bash
   sudo apt install apache2 -y
   ```

2. Crear la pàgina index per al domini:

   ```bash
   sudo nano /var/www/html/index.html
   ```

   Amb el següent contingut:

   ```html
   <!DOCTYPE html>
   <html lang="ca">
   <head>
       <meta charset="UTF-8">
       <meta name="viewport" content="width=device-width, initial-scale=1.0">
       <title>Pàgina principal</title>
   </head>
   <body>
       <h1>Benvingut a nomalumne.local!</h1>
   </body>
   </html>
   ```

3. Configurar el VirtualHost per al domini:

   ```bash
   sudo nano /etc/apache2/sites-available/000-default.conf
   ```

   Modificar el fitxer perquè inclogui:

   ```bash
   <VirtualHost *:80>
       ServerName nomalumne.local
       ServerAlias www.nomalumne.local
       DocumentRoot /var/www/html

       <Directory /var/www/html>
           Options -Indexes +FollowSymLinks
           AllowOverride All
           Require all granted
       </Directory>

       ErrorLog ${APACHE_LOG_DIR}/error.log
       CustomLog ${APACHE_LOG_DIR}/access.log combined
   </VirtualHost>
   ```

4. Reiniciar Apache perquè els canvis tinguin efecte:

   ```bash
   sudo systemctl restart apache2
   ```

Amb aquests passos, el servidor Ubuntu tindrà un DNS funcional i un servidor web que respondrà a les peticions per `nomalumne.local` i `www.nomalumne.local`.


## Segona part: Creació de dominis addicionals i Virtual Hosts

### 1. Afegir nous dominis al servidor DNS

Afegirem dos dominis més: `dominiextra.local` i `unaltredomini.local`.

1. Editar el fitxer de configuració de zones DNS:

   ```bash
   sudo nano /etc/bind/named.conf.local
   ```

   Afegir les noves zones:

   ```bash
   zone "dominiextra.local" {
       type master;
       file "/etc/bind/db.dominiextra.local";
   };

   zone "unaltredomini.local" {
       type master;
       file "/etc/bind/db.unaltredomini.local";
   };
   ```

2. Crear els fitxers de configuració de les noves zones:

   ```bash
   sudo cp /etc/bind/db.nomalumne.local /etc/bind/db.dominiextra.local
   sudo nano /etc/bind/db.dominiextra.local
   ```

   Editar el fitxer perquè contingui:

   ```bash
   ;
   ; BIND data file for dominiextra.local
   ;
   $TTL    604800
   @       IN      SOA     dominiextra.local. root.dominiextra.local. (
                         2         ; Serial
                         604800    ; Refresh
                         86400     ; Retry
                         2419200   ; Expire
                         604800 )  ; Negative Cache TTL
   ;
   @       IN      NS      dominiextra.local.
   @       IN      A       10.0.2.5
   www     IN      A       10.0.2.5
   ```

   Fer el mateix per `unaltredomini.local`:

   ```bash
   sudo cp /etc/bind/db.nomalumne.local /etc/bind/db.unaltredomini.local
   sudo nano /etc/bind/db.unaltredomini.local
   ```

   Amb el següent contingut:

   ```bash
   ;
   ; BIND data file for unaltredomini.local
   ;
   $TTL    604800
   @       IN      SOA     unaltredomini.local. root.unaltredomini.local. (
                         2         ; Serial
                         604800    ; Refresh
                         86400     ; Retry
                         2419200   ; Expire
                         604800 )  ; Negative Cache TTL
   ;
   @       IN      NS      unaltredomini.local.
   @       IN      A       10.0.2.5
   www     IN      A       10.0.2.5
   ```

3. Reiniciar el servei DNS:

   ```bash
   sudo systemctl restart bind9
   ```

### 2. Configurar Virtual Hosts a Apache

1. Crear els directoris per als nous dominis:

   ```bash
   sudo mkdir -p /var/www/dominiextra
   sudo mkdir -p /var/www/unaltredomini
   ```

2. Crear els fitxers `index.html` per a cada domini:

   ```bash
   sudo nano /var/www/dominiextra/index.html
   ```

   Amb el següent contingut:

   ```html
   <!DOCTYPE html>
   <html lang="ca">
   <head>
       <meta charset="UTF-8">
       <meta name="viewport" content="width=device-width, initial-scale=1.0">
       <title>Dominiextra</title>
   </head>
   <body>
       <h1>Benvingut a dominiextra.local!</h1>
   </body>
   </html>
   ```

   Fer el mateix per `unaltredomini.local`:

   ```bash
   sudo nano /var/www/unaltredomini/index.html
   ```

   Amb el següent contingut:

   ```html
   <!DOCTYPE html>
   <html lang="ca">
   <head>
       <meta charset="UTF-8">
       <meta name="viewport" content="width=device-width, initial-scale=1.0">
       <title>Unaltredomini</title>
   </head>
   <body>
       <h1>Benvingut a unaltredomini.local!</h1>
   </body>
   </html>
   ```

3. Crear i configurar els fitxers de Virtual Host per a cada domini:

   ```bash
   sudo nano /etc/apache2/sites-available/dominiextra.conf
   ```

   Amb el següent contingut:

   ```bash
   <VirtualHost *:80>
       ServerName dominiextra.local
       ServerAlias www.dominiextra.local
       DocumentRoot /var/www/dominiextra

       <Directory /var/www/dominiextra>
           Options -Indexes +FollowSymLinks
           AllowOverride All
           Require all granted
       </Directory>

       ErrorLog ${APACHE_LOG_DIR}/error.log
       CustomLog ${APACHE_LOG_DIR}/access.log combined
   </VirtualHost>
   ```

   Fer el mateix per `unaltredomini.local`:

   ```bash
   sudo nano /etc/apache2/sites-available/unaltredomini.conf
   ```

   Amb el següent contingut:

   ```bash
   <VirtualHost *:80>
       ServerName unaltredomini.local
       ServerAlias www.unaltredomini.local
       DocumentRoot /var/www/unaltredomini

       <Directory /var/www/unaltredomini>
           Options -Indexes +FollowSymLinks
           AllowOverride All
           Require all granted
       </Directory>

       ErrorLog ${APACHE_LOG_DIR}/error.log
       CustomLog ${APACHE_LOG_DIR}/access.log combined
   </VirtualHost>
   ```

4. Activar els Virtual Hosts i recarregar Apache:

   ```bash
   sudo a2ensite dominiextra.conf
   sudo a2ensite unaltredomini.conf
   sudo systemctl reload apache2
   ```

Ara, els tres dominis (`nomalumne.local`, `dominiextra.local` i `unaltredomini.local`) estan funcionals i servint pàgines web personalitzades.


## Tercera part: Configuració de HTTPS i certificats SSL

En aquesta secció configurarem certificats SSL per als dominis `nomalumne.local`, `dominiextra.local` i `unaltredomini.local` utilitzant OpenSSL i configurarem Apache per servir aquestes pàgines via HTTPS.

### 1. Instal·lació d'OpenSSL

Primer, assegurem-nos que OpenSSL està instal·lat:

```bash
sudo apt update
sudo apt install openssl -y
```

### 2. Creació del directori per als certificats

Crearem un directori on guardarem els certificats SSL:

```bash
sudo mkdir -p /etc/apache2/ssl
```

### 3. Generació de certificats SSL per als dominis

Per a cada domini, generarem una clau privada i un certificat autosignat.

#### Certificat per `nomalumne.local`

```bash
sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 \
-keyout /etc/apache2/ssl/nomalumne.key \
-out /etc/apache2/ssl/nomalumne.crt \
-subj "/C=ES/ST=Barcelona/L=Barcelona/O=Escola/CN=nomalumne.local"
```

#### Certificat per `dominiextra.local`

```bash
sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 \
-keyout /etc/apache2/ssl/dominiextra.key \
-out /etc/apache2/ssl/dominiextra.crt \
-subj "/C=ES/ST=Barcelona/L=Barcelona/O=Escola/CN=dominiextra.local"
```

#### Certificat per `unaltredomini.local`

```bash
sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 \
-keyout /etc/apache2/ssl/unaltredomini.key \
-out /etc/apache2/ssl/unaltredomini.crt \
-subj "/C=ES/ST=Barcelona/L=Barcelona/O=Escola/CN=unaltredomini.local"
```

### 4. Configuració dels Virtual Hosts per HTTPS

1. Crear una configuració per HTTPS de `nomalumne.local`:

   ```bash
   sudo nano /etc/apache2/sites-available/nomalumne-ssl.conf
   ```

   Amb el següent contingut:

   ```bash
   <VirtualHost *:443>
       ServerName nomalumne.local
       ServerAlias www.nomalumne.local
       DocumentRoot /var/www/html

       SSLEngine on
       SSLCertificateFile /etc/apache2/ssl/nomalumne.crt
       SSLCertificateKeyFile /etc/apache2/ssl/nomalumne.key

       <Directory /var/www/html>
           Options -Indexes +FollowSymLinks
           AllowOverride All
           Require all granted
       </Directory>

       ErrorLog ${APACHE_LOG_DIR}/error.log
       CustomLog ${APACHE_LOG_DIR}/access.log combined
   </VirtualHost>
   ```

2. Fer el mateix per `dominiextra.local`:

   ```bash
   sudo nano /etc/apache2/sites-available/dominiextra-ssl.conf
   ```

   Amb el següent contingut:

   ```bash
   <VirtualHost *:443>
       ServerName dominiextra.local
       ServerAlias www.dominiextra.local
       DocumentRoot /var/www/dominiextra

       SSLEngine on
       SSLCertificateFile /etc/apache2/ssl/dominiextra.crt
       SSLCertificateKeyFile /etc/apache2/ssl/dominiextra.key

       <Directory /var/www/dominiextra>
           Options -Indexes +FollowSymLinks
           AllowOverride All
           Require all granted
       </Directory>

       ErrorLog ${APACHE_LOG_DIR}/error.log
       CustomLog ${APACHE_LOG_DIR}/access.log combined
   </VirtualHost>
   ```

3. Finalment, per `unaltredomini.local`:

   ```bash
   sudo nano /etc/apache2/sites-available/unaltredomini-ssl.conf
   ```

   Amb el següent contingut:

   ```bash
   <VirtualHost *:443>
       ServerName unaltredomini.local
       ServerAlias www.unaltredomini.local
       DocumentRoot /var/www/unaltredomini

       SSLEngine on
       SSLCertificateFile /etc/apache2/ssl/unaltredomini.crt
       SSLCertificateKeyFile /etc/apache2/ssl/unaltredomini.key

       <Directory /var/www/unaltredomini>
           Options -Indexes +FollowSymLinks
           AllowOverride All
           Require all granted
       </Directory>

       ErrorLog ${APACHE_LOG_DIR}/error.log
       CustomLog ${APACHE_LOG_DIR}/access.log combined
   </VirtualHost>
   ```

### 5. Activació del mòdul SSL i els nous Virtual Hosts

1. Activar el mòdul SSL d'Apache:

   ```bash
   sudo a2enmod ssl
   ```

2. Activar els nous Virtual Hosts per HTTPS:

   ```bash
   sudo a2ensite nomalumne-ssl.conf
   sudo a2ensite dominiextra-ssl.conf
   sudo a2ensite unaltredomini-ssl.conf
   ```

3. Reiniciar Apache perquè els canvis tinguin efecte:

   ```bash
   sudo systemctl restart apache2
   ```

### 6. Redirigir HTTP a HTTPS

Per forçar que totes les connexions vagin per HTTPS, podem afegir una redirecció als Virtual Hosts HTTP.

Edita el fitxer de cada VirtualHost HTTP (`000-default.conf`, `dominiextra.conf` i `unaltredomini.conf`) i afegeix la següent línia dins del bloc `<VirtualHost *:80>`:

   ```bash
   Redirect permanent / https://nomalumne.local/
   ```

Fes el mateix per `dominiextra.local` i `unaltredomini.local`, canviant el domini corresponent.

Després, reinicia Apache:

   ```bash
   sudo systemctl restart apache2
   ```

---

Amb aquests passos, els dominis `nomalumne.local`, `dominiextra.local` i `unaltredomini.local` ara funcionaran amb HTTPS, utilitzant certificats SSL autosignats.


## Entregables

* Abans de la data de finalització de la pràctica, mostra al professor la teva instal·lació i comprova que tot funciona correctament.

Puja un fitxer pdf responent aquestes preguntes:

# Preguntes sobre configuració d'Apache

Aquesta secció de preguntes està enfocada en conceptes clau de la configuració d'Apache, incloent `a2ensite`, `a2enmod`, i directives importants com `ErrorLog`, `CustomLog`, `ServerName`, `ServerAlias`, `DocumentRoot` i `VirtualHost`.

## **1. Comandes per gestionar llocs i mòduls**
1. Quina és la funció de la comanda `a2ensite` en Apache?
2. Com podem desactivar un lloc web que ja està activat en Apache?
3. Per a què serveix la comanda `a2enmod`?
4. Quina comanda hem d’executar després d’activar un lloc (`a2ensite`) o un mòdul (`a2enmod`) perquè els canvis tinguin efecte?

## **2. Configuració dels Virtual Hosts**
5. Quina és la funció de la directiva `<VirtualHost *:80>` en un fitxer de configuració d'Apache?
6. Què significa `<VirtualHost *:443>` en un fitxer de configuració?
7. Per què utilitzem Virtual Hosts en un servidor Apache?
8. A quin directori es troben els fitxers de configuració dels Virtual Hosts en Ubuntu?

## **3. Directives dins d'un Virtual Host**
9. Quina diferència hi ha entre `ServerName` i `ServerAlias` en un VirtualHost?
10. Com definim el directori on Apache ha de buscar els fitxers d'un lloc web?
11. Si volem que `www.exemple.com` i `exemple.com` apuntin al mateix VirtualHost, quina configuració hauríem d’afegir?
12. Quina és la funció de la directiva `DocumentRoot`?
13. Què passa si `DocumentRoot` apunta a un directori que no existeix?

## **4. Registre i anàlisi de logs**
14. Quina informació es guarda al fitxer especificat a `ErrorLog`?
15. Quina informació es desa al fitxer definit per `CustomLog`?
16. On es troben per defecte els fitxers de registre d'Apache a Ubuntu?
17. Com podem consultar en temps real els errors d'Apache mentre es produeixen?
18. Quin és el propòsit del format `combined` en `CustomLog`?
19. Com podem personalitzar el format dels logs a Apache?

Aquestes preguntes ajudaran a consolidar els conceptes relacionats amb la configuració d'Apache i la seva administració.
