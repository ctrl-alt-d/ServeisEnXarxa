# Pràctica: Connexió Remota amb Chrome Remote Desktop

## Objectius
- Aprendre a instal·lar i configurar **Chrome Remote Desktop**.
- Crear màquines virtuals amb **Ubuntu Desktop** i **Windows**.
- Configurar-les amb **adaptador de xarxa en pont (bridged adapter)**.
- Permetre que un company es connecti remotament a les vostres màquines.

---

## Requisits previs
- Ordinador amb **VirtualBox** o un altre hipervisor instal·lat.
- Accés a **Google Chrome** amb un compte de Google.
- Fitxers ISO de **Ubuntu Desktop** i **Windows**.
- Connexió a Internet.

---

## 1. Creació de les Màquines Virtuals

Crea dues màquines virtuals per a fer les pràctiques. Connecta-les amb adaptador pont.

### Ubuntu Desktop

1. Obre **VirtualBox** i crea una nova màquina virtual:
   - Nom: `UbuntuRemote`
   - Tipus: `Linux`
   - Versió: `Ubuntu (64-bit)`
   - Memòria RAM: mínim 2048MB
   - Disc dur: mínim 20GB, VDI, dinamicament escalable

2. Afegeix el fitxer ISO d'Ubuntu com a unitat òptica i inicia la màquina.

3. Segueix l'assistent d'instal·lació d'Ubuntu fins que s'acabi la instal·lació.

4. Un cop instal·lat, apaga la màquina i ves a **Configuració > Xarxa**:
   - Adapta la connexió de xarxa a **Adaptador en pont (Bridged Adapter)**.
   - Selecciona l'adaptador de xarxa actiu del teu sistema.

5. Torna a engegar la màquina i comprova la seva IP amb:
   ```bash
   ip a
   ```

---

### Windows

1. Crea una nova màquina virtual:
   - Nom: `WindowsRemote`
   - Tipus: `Microsoft Windows`
   - Versió: segons l’ISO que tinguis (per ex. Windows 10 o 11)
   - Memòria RAM: mínim 4096MB
   - Disc dur: mínim 30GB

2. Afegeix el fitxer ISO de Windows com a unitat d’arrencada i instal·la'l.

3. Un cop instal·lat, com amb Ubuntu, configura la xarxa en **Adaptador en pont (Bridged Adapter)**.

4. Torna a iniciar i comprova la IP amb:
   ```powershell
   ipconfig
   ```

---

## 2. Tasques

* Visita la pàgina d'ajuda [Access another computer with Chrome Remote Desktop](https://support.google.com/chrome/answer/1649523?hl=en&co=GENIE.Platform%3DDesktop)
* Juntament amb un company, practiqueu els tres apartats:
   * Set up remote access to your computer
   * Share your computer with someone else
   * Access a computer remotely

* Contesta aquestes preguntes:
   * Per a quins sistemes operatius/dispositius està disponible Chrome Remote Desktop?
   * Per a quines versions de linux està disponible Chrome Remote Desktop?
   * Et pots connectar a una màquina que no tingui una IP pública a Internet? Et podries connectar al PC de casa teu?
   

## 4. Validació de la Pràctica

Per considerar la pràctica com a superada, cada alumne ha de:

- Mostrar que ha creat les dues màquines virtuals.
- Comprovar que tenen IP pròpia en la mateixa xarxa.
- Permetre la connexió d’un company via Chrome Remote Desktop.
- Documentar-ho amb captures de pantalla o vídeo breu.
- Respondre les preguntes
