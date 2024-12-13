## Configuració de DKIM amb hMailServer

hMailServer inclou suport per a DKIM, que permet signar els correus electrònics amb una clau privada per garantir l’autenticitat i la integritat dels missatges.

### 1. Preparació prèvia

1. **Accés Administratiu:** Assegura’t de tenir accés a la consola d’administració d’hMailServer.
2. **Accés DNS:** Necessites accés per configurar registres DNS al domini.

---

### 2. Generar una clau DKIM

Per a hMailServer, pots utilitzar qualsevol eina externa per generar la clau privada i la pública. Una opció és `OpenSSL`:

1. **Genera la clau privada i pública:**
   ```bash
   openssl genrsa -out dkim_private.key 2048
   openssl rsa -in dkim_private.key -pubout -out dkim_public.key
   ```

2. **Prepara la clau pública per al DNS:**
   Obre el fitxer `dkim_public.key` i copia el contingut sense cap línia de començament (`-----BEGIN PUBLIC KEY-----`) ni final (`-----END PUBLIC KEY-----`).

3. **Guarda la clau privada:**
   Assegura’t que el fitxer `dkim_private.key` es desa en una ubicació segura.

---

### 3. Configurar hMailServer

1. **Accedeix a la consola d’administració:**
   - Obre el programa **hMailServer Administrator**.
   - Ves a **Settings** > **Advanced** > **Signing**.

2. **Activa la signatura DKIM:**
   - Marca la casella **Enable DKIM signing**.

3. **Configura el domini:**
   - Selecciona el domini per al qual vols habilitar DKIM.
   - Fes clic a **Edit...**.

4. **Introduir la configuració DKIM:**
   - **Selector:** Escriu un nom identificador (p. ex., `mail` o `default`).
   - **Clau privada:** Indica la ruta completa del fitxer `dkim_private.key` que vas generar.
   - **Hashing Algorithm:** Tria `SHA-256` (recomanat).
   - **Header method:** Selecciona `Relaxed`.

5. **Aplica els canvis:**
   - Fes clic a **Save** per aplicar la configuració.

---

### 4. Configurar el registre DNS

1. Accedeix al sistema de gestió DNS del teu domini.
2. Afegeix un registre TXT amb aquesta informació:

   - **Nom:** `<selector>._domainkey.exemple.com`  
     (canvia `<selector>` pel valor usat a la configuració d’hMailServer, com `mail` o `default`).
   - **Tipus:** TXT
   - **Contingut:**
     ```
     v=DKIM1; k=rsa; p=<clau_pública>
     ```
     Substitueix `<clau_pública>` pel contingut de la clau pública generada (sense salts de línia).

3. Guarda i publica els canvis al DNS.

---

### 5. Verificar la configuració

1. Envia un correu electrònic des d’un compte configurat a hMailServer.
2. Utilitza una eina en línia com [DKIMCore](https://dkimcore.org/tools/keycheck.html) o analitza les capçaleres del correu rebut.
3. Busca una capçalera que contingui:
   ```
   DKIM-Signature: v=1; a=rsa-sha256; d=exemple.com; s=mail; ...
   ```

Amb aquests passos, hMailServer signarà correctament els correus amb DKIM, millorant la reputació i seguretat del domini.
