## 7. Pràctica Ubuntu

En aquesta pràctica, configurarem un servidor de correu electrònic a **Ubuntu** utilitzant **Mail-in-a-Box**. L’objectiu és familiaritzar-se amb la instal·lació i gestió d’un servidor de correu en entorns Linux.

### Objectius de la pràctica
1. Instal·lar i configurar **Mail-in-a-Box**.
2. Configurar dominis i comptes d'usuari.
3. Habilitar protocols de seguretat (TLS, SPF, DKIM, DMARC).
4. Utilitzar **Roundcube** per enviar i rebre correus.
5. Provar l’enviament i recepció amb un client de correu extern (Thunderbird).

---

### Requisits previs
- Una màquina amb **Ubuntu 20.04** o superior.
- Accés a Internet.
- Un domini disponible (real o fictici, per exemple `practiques.local`).
- Privilegis d'administrador (usuari `sudo`).

---

### Passos per a la pràctica

#### 7.1. Preparació del sistema

1. **Actualitza el sistema:**
   ```bash
   sudo apt update && sudo apt upgrade -y
   ```

2. **Configura el nom d'amfitrió (hostname):**
   ```bash
   sudo hostnamectl set-hostname mail.practiques.local
   ```

3. **Configura les DNS:**
   Cal que el servidor DNS resolgui el nom del servidor de correu `mail.practiques.local`

#### 7.2. Instal·lació de Mail-in-a-Box

1. **Descarrega l'script d'instal·lació:**
   ```bash
   curl -s https://mailinabox.email/setup.sh | sudo bash
   ```

2. **Segueix les instruccions:**
   - Introduïu el domini principal (`mail.practiques.local`).
   - Proporcioneu una adreça de correu electrònic d'administració.
   - Espereu que s’instal·lin tots els components necessaris (Postfix, Dovecot, etc.).

3. **Accedeix al panell d'administració:**
   - Obre un navegador i accedeix a `https://<IP-del-servidor>/admin`.
   - Inicia sessió amb les credencials configurades durant la instal·lació.

#### 7.3. Configuració del domini i comptes

1. **Configura les DNS:**
   - Afegeix els registres DNS necessaris (SPF, DKIM, DMARC) al teu domini mitjançant l'administrador de DNS.
   - El panell d'administració proporcionarà els valors exactes.

2. **Crea comptes d'usuari:**
   - Ves a **Mail Users** i crea almenys dos comptes (p. ex., `usuari1` i `usuari2`).

#### 7.4. Proves de correu electrònic

1. **Accedeix a Roundcube:**
   - Obre un navegador i accedeix a `https://<IP-del-servidor>/webmail`.
   - Inicia sessió amb un dels comptes creats.

2. **Prova l'enviament i recepció de correus:**
   - Envia un correu des de `usuari1` a `usuari2` i verifica que es rep correctament.
   - Revisa les capçaleres del correu per assegurar-te que les signatures DKIM i els registres SPF són correctes.

3. **Proves amb un client extern:**
   - Configura **Thunderbird** o un altre client amb els següents paràmetres:
     - **Servidor entrant (IMAP):** `mail.practiques.local`, port 993 (TLS).
     - **Servidor sortint (SMTP):** `mail.practiques.local`, port 587 (STARTTLS).

#### 7.5. Extensió opcional

1. **Afegir àlies de correu:**
   - Configura àlies per redirigir correus a altres comptes.

2. **Activa la còpia de seguretat:**
   - Configura les opcions de backup al panell d'administració.

3. **Monitoratge i diagnòstic:**
   - Utilitza les eines disponibles al panell per analitzar l'estat del sistema.

---

### Resultats esperats
- Els usuaris poden enviar i rebre correus electrònics utilitzant **Roundcube** i un client extern.
- El domini està configurat amb SPF, DKIM i DMARC.
- Es disposa d’una comprensió bàsica de com gestionar un servidor de correu a Linux.

---
