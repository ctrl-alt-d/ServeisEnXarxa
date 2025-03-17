# Introducció Proxy

## 1. Proxy

Un **proxy** és un servidor intermediari que actua com a pont entre un client i un servidor final. Quan un client fa una petició a un servei en línia, aquesta petició passa primer pel servidor proxy, que pot modificar, filtrar, reenviar o registrar la petició abans d'enviar-la al servidor destinatari.

### Avantatges del Proxy

- **Millora de la seguretat**: Oculta la IP real de l'usuari i pot actuar com a filtre contra contingut maliciós.
- **Anonimat i privacitat**: Evita que els llocs web coneguin la identitat real de l'usuari.
- **Control i monitorització**: Permet aplicar polítiques d'ús d'Internet en una xarxa corporativa o educativa.
- **Millora del rendiment**: Pot actuar com a memòria cau (cache) per servir continguts més ràpidament.
- **Accés a contingut restringit**: Permet accedir a serveis bloquejats en una ubicació geogràfica específica.

### Exemple d'ús

Una empresa configura un proxy per bloquejar l'accés a xarxes socials i així evitar distraccions en l'horari laboral.

## 2. Proxy Invers

Un **proxy invers** és un tipus de proxy situat davant d'un servidor per gestionar les peticions entrants. S'utilitza principalment per optimitzar el rendiment, millorar la seguretat i distribuir la càrrega de trànsit.

### Avantatges del Proxy Invers

- **Equilibri de càrrega**: Distribueix les peticions entre diversos servidors per millorar el rendiment.
- **Protecció contra atacs**: Amaga la infraestructura real del servidor i pot mitigar atacs DDoS.
- **Xifrat SSL/TLS**: Centralitza la gestió del tràfic segur HTTPS.
- **Millora del rendiment**: Pot emmagatzemar en memòria cau respostes per accelerar el lliurament de contingut.

### Exemple d'ús

Un servei de streaming utilitza un proxy invers per distribuir el trànsit de milions d'usuaris entre diferents servidors, evitant sobrecàrregues.

# Instal·lació i Configuració d'un Proxy i d'un Proxy Invers

# Configuració d'un Servidor Protegit per un Proxy

## 1. Introducció
Per millorar la seguretat d’un servidor i controlar el trànsit de xarxa, es pot configurar perquè totes les connexions passin a través d’un **servidor proxy**. Això pot ajudar a evitar accés no autoritzat, bloquejar contingut maliciós i optimitzar l’ús de la xarxa.

---

## 2. Instal·lació d'un Servidor Proxy

### **2.1. Opcions de Proxy**
Hi ha diverses opcions de servidors proxy per protegir un servidor. Alguns dels més comuns són:

- **Squid** (per a HTTP/HTTPS)
- **Nginx** (com a proxy invers)
- **HAProxy** (per a aplicacions d'alt rendiment)
- **Privoxy** (per a navegació privada)

Aquesta guia se centrarà en **Squid** i **Nginx**, ja que són àmpliament utilitzats en entorns empresarials.

---

## 3. Configuració d’un Servidor amb Squid Proxy

### **3.1. Instal·lació de Squid**
A la majoria de sistemes basats en Linux, es pot instal·lar Squid amb:

```sh
sudo apt update && sudo apt install squid -y  # Debian/Ubuntu
sudo yum install squid -y  # CentOS/RHEL
```

### **3.2. Configuració de Squid**
El fitxer de configuració principal es troba a `/etc/squid/squid.conf`. Editeu-lo amb:

```sh
sudo nano /etc/squid/squid.conf
```

Afegiu les següents línies per permetre connexions des d’un rang d’IP específic:

```conf
acl empresa_lan src 192.168.1.0/24
http_access allow empresa_lan
http_port 3128
```

Després de fer els canvis, reinicieu el servei:

```sh
sudo systemctl restart squid
sudo systemctl enable squid
```

Per comprovar que Squid funciona correctament:

```sh
curl -x http://192.168.1.1:3128 -I http://example.com
```

---

## 4. Configuració d’un Servidor amb Nginx com a Proxy Invers

Si el servidor ha d’actuar com a **proxy invers** per protegir aplicacions web, podeu utilitzar **Nginx**.

### **4.1. Instal·lació de Nginx**
```sh
sudo apt update && sudo apt install nginx -y
```

### **4.2. Configuració de Nginx com a Proxy**
Editeu la configuració de Nginx:

```sh
sudo nano /etc/nginx/sites-available/proxy
```

Afegiu aquest contingut per configurar-lo com a proxy invers:

```nginx
server {
    listen 80;
    server_name example.com;

    location / {
        proxy_pass http://backend_server_ip:backend_port;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    }
}
```

Activeu la configuració:

```sh
sudo ln -s /etc/nginx/sites-available/proxy /etc/nginx/sites-enabled/
sudo systemctl restart nginx
```

---

## 5. Afegir Autenticació al Proxy

Per protegir l'accés al proxy, podeu afegir autenticació bàsica.

### **5.1. Per a Squid**
Afegiu aquestes línies a `/etc/squid/squid.conf`:

```conf
auth_param basic program /usr/lib/squid/basic_ncsa_auth /etc/squid/passwd
auth_param basic realm Proxy Empresarial
acl autenticats proxy_auth REQUIRED
http_access allow autenticats
```

Genereu un usuari i contrasenya:

```sh
sudo apt install apache2-utils -y
sudo htpasswd -c /etc/squid/passwd usuariadmin
```

Reinicieu Squid:

```sh
sudo systemctl restart squid
```

### **5.2. Per a Nginx**
Creeu un fitxer d'usuaris:

```sh
sudo htpasswd -c /etc/nginx/.htpasswd admin
```

Afegiu l'autenticació al bloc `server` de Nginx:

```nginx
location / {
    auth_basic "Accés Restringit";
    auth_basic_user_file /etc/nginx/.htpasswd;
    proxy_pass http://backend_server_ip:backend_port;
}
```

Reinicieu Nginx:

```sh
sudo systemctl restart nginx
```

---

## 6. Verificació de la Configuració

Per assegurar-vos que el servidor utilitza el proxy, podeu provar:

- Per Squid:

```sh
curl -I --proxy http://usuariadmin:password@proxy_ip:3128 http://example.com
```

- Per Nginx:

```sh
curl -I -u admin:password http://example.com
```

Si obteniu una resposta `200 OK`, el proxy funciona correctament.

---

## 7. Conclusions
Implementar un proxy en un servidor empresarial millora la seguretat i el control del trànsit de xarxa. **Squid** és ideal per a filtratge i seguretat de navegació, mentre que **Nginx** s'utilitza per protegir aplicacions web com a proxy invers.

Amb aquesta configuració, el vostre servidor estarà protegit i només el trànsit permès passarà a través del proxy.


# Configuració dels Ordinadors dels usuaris

## 1. Introducció
Un servidor proxy s'utilitza per filtrar, monitoritzar i millorar la seguretat del trànsit de xarxa. A continuació, es mostra com configurar un proxy en sistemes Windows i macOS en un entorn empresarial.

---

## 2. Configuració en Windows

### **2.1. Configuració Manual**
1. **Obrir Configuració**  
   - Premeu `Win + I` per obrir la configuració.
   - Aneu a `Xarxa i Internet > Proxy`.

2. **Activar la configuració manual del proxy**  
   - Activeu l'opció **"Utilitza un servidor proxy"**.
   - Introduïu l'adreça IP i el port del servidor proxy proporcionat per l'empresa.
   - Feu clic a **"Desa"**.

3. **Configuració automàtica (si és necessari)**  
   - Activeu **"Detecta la configuració automàticament"** si es requereix un script de configuració (PAC).
   - Introduïu l'URL del script si cal.

---

## 3. Configuració en macOS

1. **Obrir Preferències del Sistema**  
   - Aneu a `Preferències del Sistema > Xarxa`.

2. **Seleccionar la connexió**  
   - Trieu la interfície de xarxa activa (`Wi-Fi` o `Ethernet`).
   - Feu clic a `Avançat`.

3. **Configurar el proxy**  
   - Aneu a la pestanya `Proxies`.
   - Marqueu **"Servidor proxy HTTP"** o un altre tipus segons les necessitats.
   - Introduïu l'adreça i el port del servidor proxy.

4. **Aplicar i desar**  
   - Feu clic a **D'acord** i després a **Aplica** per guardar els canvis.

---

## 4. Configuració per a Navegadors

### **Google Chrome**
1. Obriu `chrome://settings/`.
2. Aneu a `Avançat > Sistema > Obre la configuració de proxy del teu ordinador`.
3. Seguiu els passos anteriors segons el sistema operatiu.

### **Mozilla Firefox**
1. Aneu a `Menú > Configuració > General > Configuració de xarxa`.
2. Seleccioneu **Configuració manual del proxy**.
3. Introduïu l'adreça IP i el port.
4. Feu clic a **D'acord**.

---

## 5. Configuració via GPO (Windows - Empreses)
Per a entorns empresarials amb Active Directory, es pot configurar el proxy via **Group Policy Object (GPO)**.

1. Obriu `Editor de Polítiques de Grup (gpedit.msc)`.
2. Aneu a `Configuració d'usuari > Configuració de Windows > Configuració d'Internet Explorer > Connexions > Configuració del proxy`.
3. Activeu el proxy i introduïu la configuració requerida.
4. Aplicar la política als equips mitjançant Active Directory.

---

## 6. Verificació de la Configuració
Per comprovar si el proxy està funcionant correctament:
- Obriu un terminal (`cmd` o `terminal`) i executeu:

  ```sh
  curl -I --proxy http://proxy_ip:port http://example.com
  ```


# **Pràctica: Protecció d’un servidor web amb Cloudflare**

## **Introducció**
En aquesta pràctica, aprendrem a protegir un servidor web mitjançant **Cloudflare**, un servei que ofereix protecció contra atacs DDoS, optimització de rendiment i gestió de DNS avançada. Per assolir aquest objectiu, seguirem una sèrie de passos que inclouen la configuració de DNS, la creació d’una pàgina web amb **GitHub Pages** i la migració de la seva gestió a **Cloudflare**.

## **1. Disposar d’un domini amb control sobre les DNS**
Per poder gestionar la configuració de la nostra web, necessitem un **nom de domini** que ens permeti modificar els seus registres **DNS**. Un domini actua com a identificador únic a Internet i està associat a una sèrie de **registres DNS**, que indiquen a quins servidors han de dirigir-se les sol·licituds dels usuaris.

### **Conceptes clau**
- **DNS (Domain Name System)**: Servei que tradueix noms de domini en adreces IP.
- **Registres DNS**: Configuració que defineix com un domini es resol en una adreça IP.
- **Gestor de dominis**: Plataforma on podem comprar dominis i gestionar-ne la configuració.

Els alumnes hauran de disposar d’un domini propi o d’un domini de proves on puguin modificar lliurement els **registres DNS**.

---

## **2. Crear una pàgina web amb GitHub Pages**
GitHub Pages és un servei que permet allotjar pàgines web estàtiques directament des d’un repositori de **GitHub**. Aquest servei és ideal per a proves, projectes personals i webs senzilles.

### **Conceptes clau**
- **Repositori GitHub**: Espai on es guarda el codi i l’historial de versions.
- **Branch `main` o `gh-pages`**: Branch específica on s’ha de publicar la pàgina.
- **HTML/CSS/JS**: Tecnologies estàndard per a la creació de webs estàtiques.

Els alumnes hauran de crear una pàgina bàsica i activar **GitHub Pages** per al seu repositori.

---

## **3. Configurar un domini personalitzat per a GitHub Pages**
Per accedir a la web des d’un domini propi (en lloc de l’URL de GitHub), cal configurar un **custom domain** i modificar els registres DNS del domini perquè apuntin a GitHub Pages. També caldrà demostrar a GitHub que el domini és teu, això ho faràs creant un registre **TXT** amb un codi proporcionat per GitHub.

### **Conceptes clau**
- **ALIAS i CNAME Record**: Registre DNS que permet associar el domini (ex. `midomini.com`) o el subdomini (ex. `www.midomini.com`) a GitHub Pages. Més informació a [GitHub docs: DNS records for your custom domain](https://docs.github.com/en/pages/configuring-a-custom-domain-for-your-github-pages-site/managing-a-custom-domain-for-your-github-pages-site#dns-records-for-your-custom-domain)
- **A i AAAA Record**: Registre DNS que apunta a una adreça IP específica (opcional per a noms de domini sense subdomini).
- **Propagació DNS**: Procés pel qual els canvis de DNS es repliquen a tot Internet.

Els alumnes hauran de modificar els **registres DNS** en el seu gestor de dominis per fer que el seu domini personalitzat funcioni amb GitHub Pages.

---

## **4. Crear un compte a Cloudflare i migrar les DNS**
Cloudflare actua com a intermediari entre el visitant i el servidor web, oferint seguretat i millores de rendiment. Un cop tenim la nostra web en GitHub Pages amb un domini personalitzat, caldrà **migrar la gestió de DNS cap a Cloudflare**.

### **Conceptes clau**
- **Cloudflare com a CDN**: Cloudflare emmagatzema còpies en memòria cau i optimitza el trànsit.
- **Protecció DDoS**: Cloudflare pot bloquejar atacs massius a la web.
- **Migració de DNS**: Per gestionar el nostre domini amb Cloudflare, caldrà actualitzar els **nameservers** en el nostre gestor de dominis.

Els alumnes hauran de registrar-se a **Cloudflare**, afegir el seu domini i canviar els **nameservers** per apuntar cap als proporcionats per Cloudflare.

---

## **5. Activar el proxy de Cloudflare**
Cloudflare permet activar un **proxy** que oculta la IP real del servidor, protegeix contra atacs i millora el rendiment amb una capa de memòria cau.

### **Conceptes clau**
- **Proxy de Cloudflare (Icona Taronja)**: Quan el proxy està activat, el trànsit passa a través de Cloudflare abans d’arribar al servidor.
- **Mode "Full" o "Flexible"**: Defineix si la connexió entre Cloudflare i el servidor ha d’estar xifrada.
- **Optimitzacions de rendiment**: Cloudflare ofereix compressió d’imatges, minificació de codi i altres millores.

Els alumnes hauran d’activar el **proxy** de Cloudflare per a la seva pàgina i verificar que el trànsit passa correctament a través de Cloudflare.

---

## **Conclusió**
Aquesta pràctica permet als alumnes entendre com protegir una pàgina web amb **Cloudflare**, millorant la seguretat i el rendiment. Han après a:
1. Gestionar **DNS** i configurar un domini personalitzat.
2. Publicar una pàgina amb **GitHub Pages**.
3. Migrar la gestió de DNS a **Cloudflare**.
4. Activar el **proxy de Cloudflare** per millorar la protecció.

A partir d’aquí, poden explorar funcionalitats avançades de Cloudflare com **firewall rules, page rules i SSL/TLS configurations** per a més seguretat i optimització.

--

## Entregables

* Demostrar al professor que el domini està configurat correctament amb Cloudflare.
* Documentació de la pràctica amb els passos seguits i captures de pantalla.
* Apartat de conclusions amb els aprenentatges adquirits.