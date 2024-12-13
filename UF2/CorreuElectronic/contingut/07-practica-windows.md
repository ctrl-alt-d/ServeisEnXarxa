## 6. Pràctica Windows Server 2022

En aquesta pràctica, configurarem un servidor de correu utilitzant **hMailServer** en Windows Server 2022. L’objectiu és familiaritzar-se amb la configuració i gestió d’un servidor de correu en entorns Windows.

### Objectius de la pràctica
1. Instal·lar i configurar **hMailServer**.
2. Crear dominis i comptes d'usuari.
3. Configurar protocols SMTP, IMAP i POP3.
4. Habilitar la seguretat amb TLS.
5. Provar l’enviament i recepció de correus amb un client com **Thunderbird**.
6. Opcional: Configurar DKIM i SPF.

---

### Requisits previs
- Una màquina amb **Windows Server 2022** instal·lat.
- Accés a Internet.
- Un domini disponible (real o fictici, per exemple `practiques.local`).
- Certificats SSL/TLS (opcional).
- Un client de correu com **Thunderbird** instal·lat en una màquina client.

---

### Passos per a la pràctica

#### 6.1. Instal·lació d’hMailServer
1. **Descarregar hMailServer:**
   - Visita [https://www.hmailserver.com](https://www.hmailserver.com) i descarrega l'última versió.

2. **Instal·lació:**
   - Executa l’instal·lador i segueix les instruccions.
   - Tria el motor de base de dades integrat (ideal per a pràctiques).
   - Configura la contrasenya d’administrador.

#### 6.2. Configuració inicial
1. **Obrir l’administrador:**
   - Inicia **hMailServer Administrator** i connecta’t al servidor local.

2. **Crear un domini:**
   - Ves a **Domains** > **Add...**.
   - Escriu el nom del domini (p. ex., `practiques.local`) i guarda.

3. **Afegir comptes d’usuari:**
   - Selecciona el domini creat i fes clic a **Accounts > Add...**.
   - Crea almenys dues comptes (p. ex., `usuari1` i `usuari2`).

#### 6.3. Configurar protocols
1. **Activa SMTP, IMAP i POP3:**
   - Ves a **Settings > Protocols** i assegura’t que tots els protocols estan activats.

2. **Configura els ports:**
   - SMTP: Port 25 (o 587 amb TLS).
   - IMAP: Port 143 (o 993 amb TLS).
   - POP3: Port 110 (o 995 amb TLS).

#### 6.4. Habilitar TLS
1. Genera o obté un certificat SSL/TLS.
2. Ves a **Settings > Advanced > SSL Certificates**.
3. Afegeix el certificat i vincula’l al servidor.
4. Activa TLS en **Settings > Protocols**.

#### 6.5. Proves amb un client de correu
1. Configura un client com **Thunderbird**:
   - Afegeix un compte amb les credencials creades.
   - Configura els servidors entrant i sortint amb els ports corresponents.
   - Activa TLS.

2. Prova l’enviament i recepció de correus entre els usuaris.

---

### Extensió opcional
1. **Configurar DKIM:**
   - Segueix els passos descrits en l’apartat DKIM d’hMailServer.

2. **Configurar SPF:**
   - Afegeix un registre SPF al DNS del domini.

3. **Analitzar les capçaleres del correu:**
   - Verifica que les configuracions de seguretat són correctes.

---

### Resultats esperats
- Els usuaris poden enviar i rebre correus electrònics amb seguretat.
- Configuració dels protocols i certificats completada.
- Comprensió pràctica del funcionament d’un servidor de correu en Windows Server 2022.

---


## Annex: Com Crear un Certificat SSL/TLS

Els certificats SSL/TLS són necessaris per xifrar les comunicacions entre el servidor de correu i els clients. A continuació es detallen els passos per generar un certificat auto-signat o obtenir-ne un a través d’una autoritat de certificació (CA).

### Opció 1: Generar un certificat auto-signat
Un certificat auto-signat és útil per a pràctiques o entorns interns.

#### Passos:

1. **Obrir la terminal o el PowerShell:**
   - En un servidor Windows, utilitza PowerShell.
   - En un servidor Linux, utilitza la terminal.

2. **Executar el següent comandament amb OpenSSL:**
   Si no tens OpenSSL instal·lat, descarrega’l i instal·la’l des de [openssl.org](https://www.openssl.org/).

   ```bash
   openssl req -new -x509 -nodes -days 365 -newkey rsa:2048 -keyout server.key -out server.crt
   ```

3. **Omplir els camps:**
   - **Country Name (C):** Codi del país (ex: ES per Espanya).
   - **State or Province Name (ST):** Província.
   - **Locality Name (L):** Ciutat.
   - **Organization Name (O):** Nom de l’organització.
   - **Common Name (CN):** Nom del domini (ex: mail.exemple.com).

4. **Resultat:**
   - Es generen dos fitxers:
     - `server.key`: Clau privada.
     - `server.crt`: Certificat auto-signat.

5. **Integració amb el servidor de correu:**
   - Afegeix aquests fitxers a la configuració del servidor, com explicat a l’apartat de configuració TLS.

---

### Opció 2: Obtenir un certificat d’una CA (Let's Encrypt)

#### Avantatges:
- Els certificats de Let's Encrypt són gratuïts i reconeguts per la majoria de navegadors i clients de correu.

#### Passos:

1. **Instal·lar Certbot:**
   - **Ubuntu/Linux:**
     ```bash
     sudo apt update
     sudo apt install certbot
     ```
   - **Windows:** Descarrega’l des de [Certbot](https://certbot.eff.org/).

2. **Executar Certbot per al domini:**
   ```bash
   sudo certbot certonly --standalone -d mail.exemple.com
   ```
   - Substitueix `mail.exemple.com` pel domini del teu servidor de correu.

3. **Resultat:**
   - Certbot generarà els fitxers del certificat:
     - **`/etc/letsencrypt/live/mail.exemple.com/fullchain.pem`**: Certificat.
     - **`/etc/letsencrypt/live/mail.exemple.com/privkey.pem`**: Clau privada.

4. **Integració amb el servidor de correu:**
   - Afegeix aquests fitxers al servidor seguint les instruccions del programari.

5. **Renovació automàtica:**
   - Let's Encrypt requereix renovació cada 90 dies. Configura la renovació automàtica:
     ```bash
     sudo certbot renew --quiet
     ```

---

### Com verificar el certificat

1. **Verificació local:**
   Utilitza OpenSSL:
   ```bash
   openssl s_client -connect mail.exemple.com:443
   ```

2. **Eines en línia:**
   Utilitza serveis com [SSL Labs](https://www.ssllabs.com/ssltest/) per assegurar-te que el certificat està configurat correctament.

---

Amb aquests passos, podràs generar un certificat SSL/TLS i integrar-lo al servidor per garantir una comunicació segura.
