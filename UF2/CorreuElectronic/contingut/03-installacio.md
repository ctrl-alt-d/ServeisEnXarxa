## 3. Instal·lació i Gestió del Servidor de Correu Electrònic

### 3.1. Requeriments i planificació
Abans de començar amb la configuració dels servidors de correu, és necessari:
- **Hardware:** Equip o màquina virtual amb suficients recursos (mínim 2 GB de RAM i 20 GB d'emmagatzematge).
- **Sistema Operatiu:** Windows Server 2022 per al servidor Windows i Ubuntu 20.04 o superior per al servidor Linux.
- **Connexió a Internet:** Per a la configuració de dominis, certificats i proves.
- **Domini:** Necessitem un domini (real o fictici) per configurar els servidors, com `practiques.local` o un domini adquirit.
- **Accés Administratiu:** Els usuaris han de tenir permisos per instal·lar i configurar programari al sistema operatiu.

---

### 3.2. Instal·lació d'un servidor de correu Windows Server 2022

#### 3.2.1. Explicació del programa que farem servir i explicació de per què
Per aquest servidor, farem servir **hMailServer**. 
- **Per què hMailServer?**
  - És gratuït i de codi obert.
  - Ofereix compatibilitat amb els protocols SMTP, POP3 i IMAP.
  - És fàcil de configurar, fet que el fa ideal per a pràctiques educatives.
  - Inclou funcions bàsiques de seguretat com filtres antispam i integració amb antivirus.

#### 3.2.2. Resum de en què consistirà la pràctica
- **Objectiu:** Configurar un servidor de correu bàsic a Windows Server 2022 amb hMailServer.
- **Passos principals:**
  1. Instal·lació del programari hMailServer.
  2. Configuració del domini i els protocols SMTP, POP3 i IMAP.
  3. Creació de comptes d'usuari i àlies.
  4. Proves d'enviament i recepció de correus amb un client com Thunderbird.
  5. Opcional: Configuració de TLS amb certificats auto-signats.

---

### 3.3. Instal·lació d'un servidor de correu Ubuntu

#### 3.3.1. Explicació del programa que farem servir i explicació de perquè
Farem servir **Mail-in-a-Box**, una solució "tot en un" per configurar un servidor de correu electrònic.
- **Per què Mail-in-a-Box?**
  - Automatitza la configuració de Postfix, Dovecot i altres components.
  - Proporciona una interfície web fàcil d’utilitzar per gestionar comptes i dominis.
  - Inclou eines addicionals com Roundcube per a webmail i configuració automàtica de certificats TLS amb Let's Encrypt.
  - És ideal per a pràctiques ràpides i eficients en entorns educatius.

#### 3.3.2. Resum de en què consistirà la pràctica
- **Objectiu:** Instal·lar i configurar Mail-in-a-Box en Ubuntu.
- **Passos principals:**
  1. Preparació de l'entorn (actualització del sistema i configuració del domini).
  2. Instal·lació del paquet Mail-in-a-Box.
  3. Configuració inicial del domini i dels comptes d'usuari.
  4. Proves d'enviament i recepció de correus mitjançant Roundcube i un client extern.
  5. Opcional: Configuració de registres SPF, DKIM i DMARC per millorar la seguretat.

---
