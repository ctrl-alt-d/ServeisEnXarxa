## Com generar DKIM

DKIM (DomainKeys Identified Mail) és una tecnologia que permet signar digitalment els correus electrònics enviats des d'un domini per assegurar la seva autenticitat i integritat. A continuació, es descriu com generar i configurar DKIM.

### 1. Preparació del sistema

- **Servidor de correu:** Assegura’t que el servidor utilitza un programari compatible amb DKIM (Postfix, Exim, hMailServer, Mail-in-a-Box, etc.).
- **Accés administratiu:** Necessites accés per configurar els registres DNS del domini.

---

### 2. Generar la clau DKIM
Les claus DKIM consten d’una parella de claus: una clau privada (emmagatzemada al servidor de correu) i una clau pública (publicada al DNS).

#### 2.1. Amb `opendkim`
Si utilitzes `opendkim` en un servidor Linux:

1. **Crea un directori per a les claus:**
   ```bash
   mkdir -p /etc/opendkim/keys
   ```

2. **Genera la clau:**
   ```bash
   opendkim-genkey -t -s selector -d exemple.com
   ```
   - **`selector`**: Un identificador per diferenciar claus DKIM (p.ex., `mail` o `default`).
   - **`exemple.com`**: El domini per al qual configures DKIM.

3. **Fitxers generats:**
   - **`selector.private`**: Clau privada (per al servidor).
   - **`selector.txt`**: Clau pública (per al DNS).

4. **Mou la clau privada a la carpeta segura:**
   ```bash
   mv selector.private /etc/opendkim/keys/exemple.com.private
   ```

---

### 3. Configurar el servidor de correu

#### 3.1. Amb Postfix
1. Edita el fitxer de configuració `/etc/postfix/main.cf`:
   ```
   milter_default_action = accept
   milter_protocol = 6
   smtpd_milters = unix:/var/run/opendkim/opendkim.sock
   non_smtpd_milters = unix:/var/run/opendkim/opendkim.sock
   ```

2. Reinicia Postfix:
   ```bash
   systemctl restart postfix
   ```

---

### 4. Configurar el registre DNS

1. Obre el fitxer `selector.txt` generat anteriorment i copia el contingut.
   Exemple del contingut:
   ```
   selector._domainkey IN TXT "v=DKIM1; k=rsa; p=MIGfMA0GCSqG..."
   ```

2. Accedeix al teu sistema de gestió de DNS i afegeix un registre TXT amb la següent informació:
   - **Nom:** `selector._domainkey.exemple.com`
   - **Tipus:** TXT
   - **Contingut:** Tot el contingut del fitxer `selector.txt`.

3. Guarda i aplica els canvis.

---

### 5. Verificar la configuració

1. Utilitza eines en línia per verificar DKIM, com [DKIMCore](https://dkimcore.org/tools/keycheck.html).
2. Assegura’t que el registre TXT està ben configurat i que la clau pública és accessible.

---

### 6. Provar l’enviament de correus

1. Envia un correu electrònic des del teu domini configurat.
2. Revisa les capçaleres del correu i assegura’t que hi apareix una línia com:
   ```
   DKIM-Signature: v=1; a=rsa-sha256; d=exemple.com; s=selector; ...
   ```

Amb aquests passos, DKIM estarà configurat correctament al teu domini i servidor de correu, millorant la seguretat i l’autenticitat dels correus enviats.
