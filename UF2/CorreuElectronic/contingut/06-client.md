## 6. Accés al Correu Electrònic per als Usuaris

### 6.1. Configuració de clients de correu electrònic

Els clients de correu electrònic permeten als usuaris enviar, rebre i gestionar els seus correus. Els clients poden ser aplicacions d’escriptori (com **Thunderbird** o **Outlook**) o aplicacions web (com **Roundcube**).

#### Passos generals per configurar un client:
1. **Obrir el client de correu:**
   - Accedeix a les opcions de configuració o afegir un compte nou.

2. **Introduir la informació del compte:**
   - **Nom del compte:** Nom de l’usuari (exemple: usuari@exemple.com).
   - **Contrasenya:** La contrasenya associada al compte.

3. **Configuració dels servidors de correu:**
   - **Servidor entrant (IMAP o POP3):**
     - IMAP: Port 143 o 993 (TLS/SSL).
     - POP3: Port 110 o 995 (TLS/SSL).
   - **Servidor sortint (SMTP):**
     - Port 25, 587 o 465 (TLS/SSL).

4. **Configuració de seguretat:**
   - Assegura’t que els certificats TLS estan activats per garantir la seguretat de la comunicació.

5. **Prova d’enviament i recepció:**
   - Envia un correu de prova per verificar que tot funciona correctament.

#### Avantatges de clients configurats correctament:
- Experiència d’usuari més fluida.
- Possibilitat de sincronitzar correus a múltiples dispositius (amb IMAP).
- Gestió eficient d’arxius adjunts i bústies.

---

### 6.2. Recollida remota de correu electrònic

Els usuaris poden accedir al seu correu des de diferents ubicacions mitjançant serveis de recollida remota, utilitzant protocols com IMAP i POP3.

#### Diferències entre IMAP i POP3:
- **IMAP (Internet Message Access Protocol):**
  - Manté els correus al servidor i sincronitza amb el client.
  - Ideal per a usuaris que utilitzen múltiples dispositius.
  - Port per defecte: 143 (993 amb TLS).

- **POP3 (Post Office Protocol):**
  - Descarrega els correus del servidor al dispositiu local.
  - Els correus es poden eliminar del servidor després de la descàrrega.
  - Port per defecte: 110 (995 amb TLS).

#### Passos per configurar la recollida remota:
1. **Accedir al client de correu o configuració del servei web:**
   - Introduir les dades del servidor entrant (IMAP o POP3).

2. **Configuració de sincronització:**
   - Amb IMAP, assegura’t que les carpetes del servidor es sincronitzen correctament amb el client.

3. **Autenticació i seguretat:**
   - Assegura’t que l’autenticació del servidor està activada.
   - Activa el xifratge (TLS/SSL) per protegir les dades.

#### Beneficis de la recollida remota:
- Accés flexible al correu des de qualsevol ubicació.
- Sincronització automàtica entre dispositius.
- Millora de la seguretat amb protocols xifrats.

---

Amb una configuració adequada, els usuaris poden accedir de manera segura i eficient al seu correu electrònic des de qualsevol dispositiu o ubicació.
