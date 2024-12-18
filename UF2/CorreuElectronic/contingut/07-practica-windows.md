## 6. Pràctica Windows Server 2022

En aquesta pràctica, configurarem un servidor de correu utilitzant **MailEnable Free Edition** en Windows Server 2022. L’objectiu és familiaritzar-se amb la configuració i gestió d’un servidor de correu en entorns Windows.

### Objectius de la pràctica
1. Instal·lar i configurar **MailEnable Free Edition**.
2. Crear dominis i comptes d'usuari.
3. Configurar protocols SMTP, IMAP i POP3.
4. Habilitar la seguretat amb TLS.
5. Provar l’enviament i recepció de correus amb un client com **Thunderbird**.
6. Opcional: Configurar SPF i altres configuracions DNS.

---

### Requisits previs
- Una màquina amb **Windows Server 2022** instal·lat.
- Accés a Internet.
- Un domini disponible (real o fictici, per exemple `practiques.local`). Important! Has de configurar el registres MX el domini per tal que apuntin a la IP de la teva màquina! Això es fa des de l'eina de DNS:
   - Anar al teu domini.
   - Punxar en New Mail Exchange (MX)
   - Host or child domain: El teu domini,  per exemple `practiques.local`
   - Fully qualified domain name of mail server: el nom de la teva màquina que apareix al servidor de noms,  per exemple `mail.practiques.local`
- Certificats SSL/TLS (opcional).
- Un client de correu com **Thunderbird** instal·lat en una màquina client.

---

### Passos per a la pràctica

#### 6.1. Instal·lació de MailEnable

1. **Descarregar MailEnable Free Edition:**
   - Visita [https://www.mailenable.com/standard_edition.asp](https://www.mailenable.com/standard_edition.asp) i descarrega l'última versió.

2. **Instal·lació:**
   - Executa l’instal·lador i segueix les instruccions.
   - Selecciona la instal·lació bàsica (Standard Edition).
   - Defineix la contrasenya d’administrador i el domini principal (p. ex., `practiques.local`).

#### 6.2. Configuració inicial

1. **Obrir l’Eina d’Administració:**
   - Inicia **MailEnable Administrator**.

2. **Comprovar que s'ha creat el teu domini:**
   - Ves a **Messaging Manager > Nom_del_postoffice > Post Offices**.
   - Comprova que apareix el teu domini, si no apareix:
      - Fes clic dret i selecciona **New Post Office**.
      - Escriu el nom del domini (p. ex., `practiques.local`) i guarda.

3. **Afegir comptes d’usuari:**
   - Selecciona el domini creat i fes clic a **Mailboxes > New Mailbox**.
   - Crea almenys dues comptes d'usuari (p. ex., `usuari1` i `usuari2`).
   - Crea també un compte d'admin (p. ex., `usuariadmin` )

#### 6.3. Configurar protocols

1. **Activa els protocols necessaris:**
   - Ves a **Servers > Services and Connectors**.
   - Allà veuràs els diferents protocols, comprova que estan engegats: **SMTP**, **POP** i **IMAP**.

2. **Revisa els ports:**
   - Aquests són els ports per defecte:
      - SMTP: Port 25 (o 587 amb TLS).
      - IMAP: Port 143 (o 993 amb TLS).
      - POP3: Port 110 (o 995 amb TLS).
   - Veuràs que no està activat el SSL en cap dels ports. Deixa-ho sense activar.

#### 6.5. Proves amb un client de correu

1. **Configura un client com Thunderbird:**
   - Afegeix un compte amb les credencials creades.
   - Configura els servidors entrant i sortint amb els ports corresponents.
   - No activis TLS.

2. **Prova l’enviament i recepció de correus entre els usuaris:**
   - Envia un correu de `usuari1` a `usuari2` i verifica que es rep correctament.

---
#### 6.4. Habilitar TLS

1. **Generar o obtenir un certificat SSL/TLS:**
   - Segueix l’[annex per crear un certificat](#annex-com-crear-un-certificat-ssl-tls).

2. **Configura el certificat a MailEnable:**
   - Ves a **Servers > Localhost > Properties > SSL**.
   - Afegeix el certificat generat i selecciona’l per als protocols.

3. **Activa TLS:**
   - Configura l’ús de TLS a SMTP, IMAP i POP des de les opcions de cada servei.

4. **Connecta amb SSL**
   - Modifica els paràmetres de Thunderbird per tal de realitzar les connexions IMAP i SMTP amb TLS.


### Extensió opcional

1. **Configurar SPF:**
   - Afegeix un registre SPF al DNS del domini amb una configuració bàsica com:
     ```
     v=spf1 mx -all
     ```

2. **Configurar àlies:**
   - Configura àlies de correu a **MailEnable Administrator** per redirigir correus a altres comptes.

3. **Analitzar les capçaleres del correu:**
   - Revisa les capçaleres per assegurar que la configuració és correcta.

---

### Resultats esperats
- Els usuaris poden enviar i rebre correus electrònics amb seguretat.
- Configuració dels protocols i certificats completada.
- Comprensió pràctica del funcionament d’un servidor de correu amb MailEnable en Windows Server 2022.

---


## Annex: Com crear un certificat SSL TLS

* Revisa la documentació de MailEnable: https://www.mailenable.com/documentation/10.0/Standard/Localhost_-_Secure_Sockets_Layer_(SSL)_encryption.html


## Com Crear un Certificat SSL/TLS per a MailEnable a Windows

Per habilitar la seguretat TLS a MailEnable, necessites un certificat SSL/TLS. A continuació s'explica com generar un certificat auto-signat o obtenir-ne un certificat reconegut per una autoritat de certificació (CA).

---

### Generar un certificat auto-signat
Un certificat auto-signat és útil per a pràctiques o entorns interns, però no serà reconegut pels navegadors ni els clients de correu.

#### Passos:

1. **Obrir el Microsoft Management Console (MMC):**
   - Escriu `mmc` a la barra de cerca de Windows i prem Enter.

2. **Afegir el Snap-in de certificats:**
   - A `File > Add/Remove Snap-in...`, selecciona **Certificates** i fes clic a **Add**.
   - Tria **Computer Account** > **Local Computer**.

3. **Generar un certificat auto-signat:**
   - Ves a **Certificates (Local Computer) > Personal > Certificates**.
   - Fes clic dret a **Certificates** i selecciona **All Tasks > Request New Certificate...**.
   - Selecciona **Active Directory Enrollment Policy** i segueix els passos fins que aparegui l'opció **Create and Submit a Request to This CA**.
   - Omple els detalls necessaris, com el nom comú (**Common Name**) que ha de coincidir amb el nom del domini (p. ex., `mail.exemple.com`).

4. **Exporta el certificat:**
   - Selecciona el certificat generat i fes clic dret > **All Tasks > Export...**.
   - Tria **Exportar la clau privada** i desar el certificat amb el format `.pfx`.
   - Defineix una contrasenya per protegir la clau privada.

5. **Integració amb MailEnable:**
   - Afegeix el certificat exportat al servidor seguint els passos indicats més avall.


### Configurar el Certificat a MailEnable

1. **Obrir MailEnable Administrator:**
   - Ves a **Servers > Localhost > Properties**.

2. **Afegir el certificat:**
   - A la pestanya **SSL**, selecciona **Install Certificate...**.
   - Busca el fitxer `.pfx` exportat i introdueix la contrasenya si cal.

3. **Assignar el certificat als protocols:**
   - Torna a **Servers > Services and Connectors**.
   - Selecciona **SMTP, IMAP o POP**, ves a les propietats i habilita l’ús de TLS/SSL amb el certificat.

4. **Reinicia els serveis:**
   - Reinicia els serveis afectats per assegurar-te que el certificat s’aplica correctament.

---
