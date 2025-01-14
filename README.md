# Sblocco Telnet per ZTE F6005 (prima versione)

Questa guida descrive come sbloccare Telnet sugli ONT **ZTE F6005** (prima versione, con la scocca dagli spigoli arrotondati). Verrà utilizzato un dump modificato (`UNLOCKEDTELNET.bin`) da flashare direttamente sulla SPI NOR Flash del dispositivo.

Il file `rootfs.img` contiene il firmware estratto dal dump modificato ed è utilizzabile direttamente per l'upgrade da da web-gui (vedere il capitolo *Upgrade tramite web-gui*).

---

## ⚠️ Avvertenze

1. **Ho creato personalmente la modifica**, senza attingere ad alcun file creato da terzi. Il lavoro è stato molto impegnativo, se si vuole approfondire il mio studio ho creato un'altra repository in cui ho documentato tutto: [ZTE-F6005-Modding](https://github.com/rgiorgiotech/ZTE-F6005-Modding).
2. **Compatibilità**: il dump fornito è compatibile **solo** con gli ONT ZTE F6005 "prima versione".
3. **Non sono responsabile di eventuali danni o malfunzionamenti causati dalla procedura descritta**. Prima di proseguire, assicurarsi di sapere cosa si sta facendo e di comprendere i rischi associati.

---

## 🔧 Strumenti necessari

### Hardware
- Programmer CH341A;
- Clip SOIC8 (opzionale): utile per flash senza dissaldatura;
- Computer con Linux (OK live ISO) o macOS.

### Software
- **flashrom**:
  - Su **macOS**: installabile tramite Homebrew con il comando:
    ```bash
    brew install flashrom
    ```
  - Su **Linux**: installabile tramite il package manager della propria distribuzione.

---

## 🚀 Procedura

### 1. Preparazione dell'ONT
1. **Disconnessione dell'alimentatore**: l'ONT deve scollegato dall'alimentazione.
2. **Isolamento del pin 8 (VCC)**: se viene utilizzata una clip SOIC8 per leggere o scrivere, probabilmente è necessario staccare il pin 8 (VCC, è l'ultimo pin in senso antiorario partendo dal pin 1, che è solitamente indicato con un cerchietto accanto vicino al chip).
3. **Backup del chip**: effettuare un backup con il seguente comando:
   ```bash
   flashrom -p ch341a_spi -r backup.bin
   ```

### 2. Flash del dump
1. Posizionarsi nella directory contenente il file `UNLOCKEDTELNET.bin` (scaricabile da questa repository).
2. Collegare il programmatore CH341A al computer.
3. Eseguire il seguente comando:
   ```bash
   flashrom -p ch341a_spi -w UNLOCKEDTELNET.bin
   ```
4. Opzioni aggiuntive per il comando:
   - se richiesto dal software, specificare il modello del proprio chip con `-c MODELLO`
   - è possibile aggiungere `-VVV` per visualizzare i byte letti e scritti
  
### 3. Verifica del flash (opzionale ma consigliato)
Dopo il flash, è possibile leggere il contenuto del chip e confrontarlo con il dump scaricato per verificare la correttezza della procedura. Flashrom dovrebbe già fare un check di default dopo aver scritto sul chip.
1. Leggere il nuovo contenuto del flash:
   ```bash
   flashrom -p ch341a_spi -r VERIFYDUMP.bin
   ```
2. Confrontare il file `UNLOCKEDTELNET.bin` con `VERIFYDUMP.bin` utilizzando un comando come:
   ```bash
   diff UNLOCKEDTELNET.bin VERIFYDUMP.bin
   ```
Se non ci sono differenze (nessun output del comando `diff`), il flash è stato eseguito correttamente.

---

## Upgrade tramite web-gui

Se l'ONT permette l'aggiornamento del firmware tramite web-gui, è possibile usare il file `rootfs.img` (scaricabile da questa repository) per sbloccare Telnet senza accedere alla SPI NOR flash. Se non viene visualizzata la pagina "Software Upgrade" nell'interfaccia web dell'ONT, l'unica procedura valida resta quella descritta prima.

**Attenzione**: questa procedura non è stata testata approfonditamente.

---

## Ringraziamenti
Si ringrazia Skizzo per l'aiuto sul modding, [FedeBertos](https://github.com/FedeBertos) e Axtermax per i test (andati a buon fine) di tale procedura.

---

Per eventuali dubbi o problemi, basta aprire una issue nella repository.
