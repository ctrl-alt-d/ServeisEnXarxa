## 2. Protocols del Correu Electrònic
   - 2.1. Protocols per a la tramesa de correu electrònic (SMTP)
   - 2.2. Protocols per a la recepció de correu electrònic (POP3, IMAP)
   - 2.3. Protocols de seguretat (SSL/TLS, STARTTLS)

### 2. Protocols del Correu Electrònic

#### 2.1. Protocols per a la tramesa de correu electrònic (SMTP)
El Simple Mail Transfer Protocol (SMTP) és el protocol estàndard utilitzat per enviar correus electrònics. Es basa en un model client-servidor i estableix com els missatges són enviats des del client fins al servidor de correu del destinatari.

```
[Client]
    |
    |---SMTP---> [Servidor d'origen] ---SMTP---> [Servidor destinatari]
                                        |
                                        |---POP3/IMAP---> [Client destinatari]
```

#### 2.2. Protocols per a la recepció de correu electrònic (POP3, IMAP)
- **Post Office Protocol (POP3):** És un protocol senzill per descarregar correus des del servidor al client. Els missatges normalment es descarreguen i s'eliminen del servidor.
- **Internet Message Access Protocol (IMAP):** Permet mantenir els correus al servidor i sincronitzar múltiples dispositius amb el mateix compte, oferint més flexibilitat que POP3.

```
[Servidor]
    |
   POP3/IMAP
    |
[Client]
```

#### 2.3. Protocols de seguretat (SSL/TLS, STARTTLS)
- **SSL/TLS:** Afegeixen un xifratge al trànsit de correu electrònic, assegurant que les dades no puguin ser interceptades durant el transport.
- **STARTTLS:** Permet actualitzar una connexió no segura a una connexió segura mitjançant TLS.

Els protocols de seguretat són essencials per protegir la privadesa dels usuaris i evitar atacs com la intercepció de missatges.

| Protocol | Port Estàndard | Port amb SSL/TLS Implícit |
|----------|----------------|--------------------------|
| IMAP     | 143            | 993                      |
| POP      | 110            | 995                      |
| SMTP     | 25             | 465                      |


## Com es creen certificats per a correu electrònic

Els certificats SSL/TLS asseguren la comunicació entre el servidor de correu i els clients, evitant la intercepció de dades. A continuació, s'explica com crear un certificat:

### 1. Generar una CSR (Certificate Signing Request)
Una CSR és un fitxer que conté informació del domini i l'organització. Per crear-lo:

1. Executa el següent comandament al servidor:

```
openssl req -new -newkey rsa:2048 -nodes -keyout server.key -out server.csr
```

2. Omple els camps requerits:
   - **Country Name (C):** Codi del país (ex: ES per Espanya).
   - **State or Province Name (ST):** Província.
   - **Locality Name (L):** Ciutat.
   - **Organization Name (O):** Nom de l'organització.
   - **Common Name (CN):** Nom del domini (ex: mail.exemple.com).

Aquest procés genera dos fitxers:
- **server.key:** Clau privada.
- **server.csr:** Sol·licitud de certificat.

### 2. Obtenir el certificat

#### Opció A: Certificat auto-signat (només per proves)
1. Genera el certificat:

```
openssl x509 -req -days 365 -in server.csr -signkey server.key -out server.crt
```

2. Aquest fitxer **server.crt** serà el certificat.

#### Opció B: Certificat d'una Autoritat de Certificació (CA)
1. Envia el fitxer `server.csr` a una CA reconeguda (ex: Let's Encrypt, DigiCert).
2. La CA verificarà la propietat del domini i emetrà un certificat.
3. Rebràs fitxers com:
   - **Certificat (server.crt)**
   - **Certificat intermedi (intermediate.crt)**

### 3. Configuració del servidor de correu
Integra els certificats al teu servidor de correu (ex: Postfix o Dovecot):

#### Postfix (SMTP):
1. Edita el fitxer de configuració (`/etc/postfix/main.cf`):

```
smtpd_tls_cert_file=/path/to/server.crt
smtpd_tls_key_file=/path/to/server.key
smtpd_tls_CAfile=/path/to/intermediate.crt
```

2. Reinicia Postfix:

```
systemctl restart postfix
```

#### Dovecot (IMAP/POP):
1. Edita el fitxer de configuració (`/etc/dovecot/conf.d/10-ssl.conf`):

```
ssl_cert = </path/to/server.crt
ssl_key = </path/to/server.key
ssl_ca = </path/to/intermediate.crt
```

2. Reinicia Dovecot:

```
systemctl restart dovecot
```

### 4. Verificació del certificat

1. Utilitza eines com `openssl` per validar:

```
openssl s_client -connect mail.exemple.com:443
```

2. Assegura't que el certificat és reconegut i no té errors.

Amb aquests passos, el servidor de correu estarà configurat amb un certificat SSL/TLS segur.

