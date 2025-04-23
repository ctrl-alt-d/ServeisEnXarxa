# Pràctica: Connexió segura amb VPN usant OpenVPN

## 🎯 Objectiu
Configurar una VPN amb **OpenVPN** que permeti a un **client Windows** accedir a un **servidor web (Ubuntu)** a través d’un **servidor VPN (Ubuntu)** en un entorn virtualitzat amb **VirtualBox**.

---

## 🧱 Infraestructura de xarxa

| Màquina | Sistema Operatiu | Rol           | Xarxa VirtualBox           |
|---------|------------------|----------------|-----------------------------|
| A       | Windows          | Client VPN     | xarxa1                      |
| B       | Ubuntu Server    | Servidor VPN   | xarxa1, xarxa2              |
| C       | Ubuntu Server    | Servidor web   | xarxa2                      |

- `xarxa1`: NAT interna → A + B  
- `xarxa2`: NAT interna → B + C

---

## 🧰 Fase 1: Configurar el servidor web (Màquina C)

1. Instal·la Apache:
   ```bash
   sudo apt update
   sudo apt install apache2 -y
   ```

2. Edita la pàgina d’inici:
   ```bash
   echo "Servidor web de la màquina C" | sudo tee /var/www/html/index.html
   ```

3. Comprova des de la màquina B:
   ```bash
   curl http://IP_C
   ```

---

## 🛡️ Fase 2: Instal·lar i configurar el servidor OpenVPN (Màquina B)

### Instal·lació de paquets:
```bash
sudo apt update
sudo apt install openvpn easy-rsa -y
```

### Configura la infraestructura de claus (PKI)

En aquest apartat prepararem tot el necessari per utilitzar certificats i claus per a l'autenticació segura entre el servidor i els clients. OpenVPN utilitza una infraestructura de clau pública (PKI) que inclou:

- Una **Autoritat Certificadora (CA)** pròpia que signarà els certificats.
- Un **certificat i clau privada per al servidor**.
- Un **certificat i clau privada per a cada client**.
- Una clau de seguretat addicional (TA) per reforçar la connexió TLS.

---

#### **1. Crear el directori de treball per Easy-RSA**

```bash
sudo make-cadir /etc/openvpn/easy-rsa
cd /etc/openvpn/easy-rsa
```

> Aquesta comanda crea una còpia del directori `easy-rsa` amb tots els scripts i fitxers necessaris per gestionar certificats. Ho fem a `/etc/openvpn/` per mantenir-ho organitzat i independent de canvis en el paquet.

---

#### **2. Inicialitzar la infraestructura de claus**

```bash
./easyrsa init-pki
```

> Aquesta comanda crea l’estructura de directoris `pki/` on es guardaran totes les claus i certificats generats (tant del servidor com dels clients). És el punt de partida abans de poder generar res.

---

#### **3. Crear la clau i certificat de la CA**

```bash
./easyrsa build-ca
```

> Aquí generem la clau privada i el certificat de la **CA (Certificate Authority)**, que és com el “segell oficial” que després servirà per signar tant el certificat del servidor com el dels clients. Et demanarà un nom de la CA i una contrasenya (passphrase) que hauràs de recordar per operacions futures com la signatura de certificats.

---

#### **4. Generar la clau privada i petició de certificat per al servidor**

```bash
./easyrsa gen-req myserver nopass
```

> Aquesta comanda genera:
> - Una clau privada per al servidor (`myserver.key`)
> - Una petició de certificat (`myserver.req`) que s’ha de signar amb la CA

Utilitzem l’opció `nopass` per evitar haver d’introduir la contrasenya cada cop que s’arrenca el servidor OpenVPN.

---

#### **5. Signar la petició del servidor amb la CA**

```bash
./easyrsa sign-req server myserver
```

> Ara la CA valida i signa la petició de certificat (`.req`) del servidor, creant així el certificat definitiu (`myserver.crt`) que podrà ser usat pel servidor VPN per identificar-se.

---

#### **6. Generar paràmetres Diffie-Hellman**

```bash
./easyrsa gen-dh
```

> Aquesta comanda genera el fitxer `dh.pem`, necessari per establir connexions segures. S'utilitza per l’intercanvi de claus de manera segura entre client i servidor.

---

#### **7. Generar la clau TLS d’autenticació (opcional però recomanada)**

```bash
sudo openvpn --genkey --secret ta.key
```

> Aquesta clau (`ta.key`) afegeix una capa extra de seguretat TLS mitjançant el mètode HMAC. Evita connexions malicioses que intentin iniciar la comunicació sense tenir aquesta clau.

---

#### **8. Copiar els fitxers necessaris al directori del servidor**

```bash
sudo cp pki/ca.crt pki/issued/myserver.crt pki/private/myserver.key pki/dh.pem ta.key /etc/openvpn/
```

> Ara traslladem tots els fitxers que el servidor OpenVPN necessita cap al directori principal `/etc/openvpn/`, on el servei podrà llegir-los fàcilment quan s'iniciï.

---



### Configura el servidor:
1. Copia i descomprimeix un fitxer de mostra:
   ```bash
   sudo cp /usr/share/doc/openvpn/examples/sample-config-files/server.conf.gz /etc/openvpn/myserver.conf.gz
   sudo gzip -d /etc/openvpn/myserver.conf.gz
   ```

2. Edita `/etc/openvpn/myserver.conf`:
   Verifica aquestes línies:

ca ca.crt cert myserver.crt key myserver.key dh dh.pem tls-auth ta.key 0


### Activa el forwarding IP:
```bash
sudo nano /etc/sysctl.conf
# descomenta:
net.ipv4.ip_forward=1
sudo sysctl -p /etc/sysctl.conf
```

> Pots afegir una regla NAT si cal (interfície de sortida: `ethX`):
```bash
sudo iptables -t nat -A POSTROUTING -s 10.8.0.0/24 -o ethX -j MASQUERADE
```

### Inicia el servei:
```bash
sudo systemctl start openvpn@myserver
sudo systemctl enable openvpn@myserver
```

L'enable és per tal que s'inicii el servei quan s'inicii la màquina
---

## 🖥️ Fase 3: Crear i configurar el client (Màquina A - Windows)

### Generació de certificats del client (des de màquina B):
```bash
cd /etc/openvpn/easy-rsa
./easyrsa gen-req myclient1 nopass
./easyrsa sign-req client myclient1
```

Copia al client els fitxers:
- `pki/ca.crt`
- `pki/issued/myclient1.crt`
- `pki/private/myclient1.key`
- `ta.key`

### Crea el fitxer `myclient1.ovpn`:
Contingut recomanat:

```
client
dev tun
proto udp
remote IP_B 1194
resolv-retry infinite
nobind
persist-key
persist-tun
ca ca.crt
cert myclient1.crt
key myclient1.key
tls-auth ta.key 1
cipher AES-256-CBC
verb 3
```

### Instal·la i configura el client:
1. Descarrega **OpenVPN Connect** o **OpenVPN GUI**.
2. Importa `myclient1.ovpn`.

---

## 🧪 Fase 4: Validació de la connexió

1. Inicia sessió VPN des del client.
2. Comprova que es crea la interfície `tun0`:
```bash
ipconfig (Windows)
ip addr show dev tun0 (Linux)
```

3. Prova connexions:
- Fes ping a `10.8.0.1` (servidor VPN)
- Fes ping o obre `http://IP_C` (màquina C)

---

## ✅ Comprovacions finals

- [ ] El client es connecta correctament a la VPN.
- [ ] El client pot fer ping a la màquina C.
- [ ] El navegador mostra la pàgina web de la màquina C.

---

## 🛠️ Troubleshooting

- Revisa els logs:
```bash
sudo journalctl -u openvpn@myserver -xe
```

- Comprova que el tallafoc no bloqueja el port UDP 1194.
- Comprova rutes amb `ip route` (Linux) o `route print` (Windows).
- Verifica la configuració de certificats i fitxers.

---

## 📎 Annex: Exemples d’IP

- Màquina A (xarxa1): `10.0.2.6`
- Màquina B (xarxa1): `10.0.2.5`
- Màquina B (xarxa2): `10.0.3.5`
- Màquina C (xarxa2): `10.0.3.6`
- Interfície tun0 del servidor: `10.8.0.1`
- Interfície tun0 del client: `10.8.0.6`

---

## 📘 Recursos addicionals

- [How to install and use OpenVPN](https://documentation.ubuntu.com/server/how-to/security/install-openvpn/index.html)
- [openvpn-gui](https://github.com/OpenVPN/openvpn-gui?tab=readme-ov-file)

---

## Lliurables

* Fitxers de configuració més importants.
* Captura de pantalla Màquina A amb el tunel establert.
* Captura de pantalla de la taula de rutes de Màquina A amb el tunel establert.
