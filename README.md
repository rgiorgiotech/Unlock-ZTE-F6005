# Sblocco Telnet per ZTE F6005 (prima versione)

Questa guida descrive come sbloccare Telnet sugli ONT **ZTE F6005** (prima versione, con la scocca dagli spigoli arrotondati). Verrà utilizzato un dump modificato da flashare direttamente sulla SPI NOR Flash del dispositivo.

---

## ⚠️ Avvertenze
1. **Ho creato personalmente il dump modificato**, senza attingere ad alcun file creato da terzi o informazioni già presenti altrove. Il lavoro è stato molto impegnativo, se si vuole approfondire il mio studio ho creato un'altra repository in cui ho documentato tutto: [ZTE-F6005-Modding](https://github.com/rgiorgiotech/ZTE-F6005-Modding).
2. **Compatibilità**: il dump fornito è compatibile **solo** con gli ONT ZTE F6005 "prima versione".
3. **Backup**: prima di flashare il dump fornito, è consigliabile fare un dump del contenuto originale del chip.

---

## 🔧 Strumenti necessari

### Hardware
- Programmer CH341A;
- Clip SOIC8 (opzionale): utile per flash senza dissaldatura.

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
2. **Isolamento del pin 8 (VCC)**: Se viene utilizzata una clip SOIC8 per leggere o scrivere, probabilmente è necessario staccare il pin 8 (VCC).

### 2. Flash del dump
1. Posizionarsi nella directory contenente il file `UNLOCKEDTELNET.bin` (scaricabile da questa repository).
2. Collegare il programmatore CH341A al computer.
3. Eseguite il seguente comando:
   ```bash
   flashrom -p ch341a_spi -w UNLOCKEDTELNET.bin
   ```
4. Opzioni aggiuntive per il comando:
   - se richiesto dal software, specificare il modello del proprio chip con `-c MODELLO`
   - è possibile aggiungere `-VVV` per visualizzare i byte letti e scritti
  
### 3. Verifica del flash (opzionale ma consigliato)
1. Dopo il flash, è possibile leggere nuovamente il contenuto del chip e verificare la correttezza del processo:
   ```bash
   flashrom -p ch341a_spi -r VERIFYDUMP.bin
   ```
2. Confrontare quindi il file `UNLOCKEDTELNET.bin` con `VERIFYDUMP.bin` utilizzando un comando come:
   ```bash
   diff UNLOCKEDTELNET.bin VERIFYDUMP.bin
   ```
   Se non ci sono differenze (nessun output del comando `diff`), il flash è stato eseguito correttamente.

---

## 💡 Note finali
1. **Software alternativi**: sconsiglio l'utilizzo di Windows con software come ASProgrammer.
2. **Persistenza delle modifiche**: una volta flashato il dump, le modifiche per sbloccare Telnet saranno permanenti, anche dopo lo spegnimento.
3. **SSH e porta seriale**: non ho sbloccato queste funzioni, l'unica funzione accessibile è Telnet.

---

Per eventuali dubbi o problemi, basta aprire una issue nella repository.
