# Pràctica 1: Accés remot per terminal amb SSH

## Objectius

- Instal·lar i configurar el servei SSH en un sistema Linux.
- Entendre el funcionament bàsic del protocol SSH.
- Connectar-se remotament mitjançant terminal des d’un altre equip Linux.
- Connectar-se remotament mitjançant terminal des d’un altre equip Windows.
- Verificar l'estat del servei i gestionar usuaris.
- Millorar la seguretat amb configuracions bàsiques.

---

## Requisits previs

- Dos equips amb Linux instal·lat (al mennys un d'ells ha de ser server).
- Un equip amb Windows (desktop) instal·lat
- Connexió de xarxa entre els tres equips (real o virtual).
- Accés amb permisos d’administrador (usuari amb `sudo`).

---

## Passos a seguir

### 1. Instal·lació del servei SSH

En el sistema que actuarà com a **servidor** (equip al qual ens connectarem):

'''bash
sudo apt update
sudo apt install openssh-server
'''

Per comprovar si el servei està actiu:

'''bash
sudo systemctl status ssh
'''

En cas que no estigui actiu, l’activem:

'''bash
sudo systemctl enable --now ssh
'''

---

### 2. Verificació de la IP

Cal saber la IP del servidor per connectar-nos-hi des del client:

'''bash
ip a
'''

Apunta la IP local del dispositiu (ex: `192.168.1.105`).

---

### 3. Connexió des del client

Des de l’altre equip (el **client**), obre un terminal i executa:

'''bash
ssh nom_usuari@ip_del_servidor
'''

Exemple:

'''bash
ssh alumne@192.168.1.105
'''

Accepta la connexió i introdueix la contrasenya quan es demani.

---

### 4. Proves i comandes bàsiques

Un cop connectat, prova comandes bàsiques com:

'''bash
whoami
hostname
uptime
'''

---

### 5. Gestió d'usuaris remots

Prova d’afegir un usuari des del client SSH:

'''bash
sudo adduser prova
'''

Després, tanca la sessió amb:

'''bash
exit
'''

I intenta accedir com el nou usuari:

'''bash
ssh prova@192.168.1.105
'''

---

### 6. Configuració bàsica del servidor SSH

Edita el fitxer de configuració:

'''bash
sudo nano /etc/ssh/sshd_config
'''

Algunes opcions recomanades a revisar:

- Canviar el port per defecte (22)
- Desactivar l'accés a root: `PermitRootLogin no`
- Activar només usuaris concrets: `AllowUsers alumne`

Després de fer canvis:

'''bash
sudo systemctl restart ssh
'''

---


---

### 6 Connexió amb claus pública/privada (opcions extres)

#### Què és?
SSH permet connectar-se de manera segura sense utilitzar contrasenyes, mitjançant un parell de claus: **una clau pública** (que s’envia al servidor) i **una clau privada** (que queda al client).

Aquest mètode és **més segur i pràctic**, sobretot si s’automatitzen connexions.

---

#### 1. Generar les claus al client

Al dispositiu **client** (des d’on et vols connectar), executa:

'''bash
ssh-keygen
'''

Prem `Enter` a totes les preguntes per acceptar la ruta per defecte i deixar la clau sense contrasenya (per ara).

Aquest procés generarà dos fitxers dins de `~/.ssh/`:

- `id_rsa` → **clau privada** (NO compartir mai)
- `id_rsa.pub` → **clau pública** (es pot copiar al servidor)


Nota: a Windows ho pots fer des de la powershell.
---

#### 2. Copiar la clau pública al servidor

Utilitza la següent comanda:

'''bash
ssh-copy-id nom_usuari@ip_del_servidor
'''

Exemple:

'''bash
ssh-copy-id alumne@192.168.1.105
'''

Et demanarà la contrasenya **una sola vegada**. Després d’això, el servidor ja guardarà la clau pública a `~/.ssh/authorized_keys`.

---

#### 3. Provar la connexió

Ara pots connectar-te **sense que et demani contrasenya**:

'''bash
ssh alumne@192.168.1.105
'''

---

#### 4. Opcional: Desactivar l’accés per contrasenya (seguretat extra)

Per obligar l’ús de claus i **evitar connexions amb contrasenya**, edita el fitxer del servidor:

'''bash
sudo nano /etc/ssh/sshd_config
'''

I canvia (o afegeix) les següents línies:

'''bash
PasswordAuthentication no
PubkeyAuthentication yes
'''

Després, reinicia el servei:

'''bash
sudo systemctl restart ssh
'''

⚠️ **Important:** Assegura’t que pots connectar-te amb la clau abans de desactivar les contrasenyes!

---


---

### 7 🔌 Connexió al servidor SSH des de Windows amb **PuTTY**

**PuTTY** és un client SSH molt utilitzat a Windows. Permet connectar-te de manera segura a un servidor Linux mitjançant accés remot per terminal. A continuació t'expliquem com fer-ho.

---

#### 🔽 1. Instal·la PuTTY

- Descarrega PuTTY des de la web oficial:  
  [https://www.putty.org](https://www.putty.org)

- Instal·la’l o executa directament el fitxer `.exe`.

---

#### 🌐 2. Obtenir la IP del servidor Linux

Assegura’t que el servidor Linux tingui el servei SSH en funcionament i obté la seva IP amb:

'''bash
ip a
'''

Apunta l’adreça IP local (ex: `192.168.1.105`).

---

#### 🔑 3. Accedir amb nom d’usuari i contrasenya

1. Obre **PuTTY**.
2. A l’apartat `Host Name (or IP address)`, escriu la IP del servidor Linux.
3. Assegura’t que el `Port` és **22** i el `Connection type` és **SSH**.
4. Clica `Open`.

S’obrirà una finestra de terminal on et demanarà el **nom d’usuari** i la **contrasenya**.

---

#### 🔐 4. Accedir amb clau privada (si no vols fer servir contrasenya) Opcional

Per usar una **clau privada**, primer cal convertir-la al format de PuTTY:

##### 4.1. Convertir la clau privada (de `.pem` o `.id_rsa` a `.ppk`)

1. Obre l'eina **PuTTYgen** (instal·lada amb PuTTY).
2. Clica el botó `Load` i selecciona el fitxer `id_rsa` (has d’escollir “All Files” per veure’l).
3. Clica `Save private key` i desa’l com `.ppk`.

##### 4.2. Usar la clau amb PuTTY

1. Torna a obrir **PuTTY**.
2. A `Host Name`, escriu la IP del servidor.
3. Ves a **Connection > SSH > Auth**.
4. A l’apartat `Private key file for authentication`, selecciona el fitxer `.ppk` que has desat.
5. Torna al menú inicial i clica `Open`.

Et demanarà només el **nom d’usuari**, no la contrasenya (si tot està ben configurat).



## Activitats a lliurar

1. Captura de pantalla de la connexió SSH des dels clients.
2. Llistat de les comandes executades remotament.
3. Explicació de les opcions modificades al fitxer `sshd_config`.
4. Reflexió breu: avantatges i inconvenients de treballar amb SSH.
5. Captura de la generació de claus.
6. Captura de la connexió sense contrasenya.
7. Contingut del fitxer `~/.ssh/authorized_keys` (al servidor).

---

