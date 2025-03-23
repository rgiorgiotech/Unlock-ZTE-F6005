# Sblocco Telnet ZTE F6005 ONT (prima versione)

In questa guida è illustrato come sbloccare Telnet sull'ONT ZTE F6005 V6 (prima versione, angoli arrotondati). Un dump modificato (`unlocked-dump.bin`) sarà flashato direttamente nel chip SPI del dispositivo.

Il file `newrootfs.img` può essere usato per l'aggiornamento diretto dall'interfaccia web o, se è già sbloccato, da Telnet (vedere relative sezioni).

Se questo progetto ti è stato utile e vuoi offrirmi un caffè ☕️ o una pizza 🍕, puoi farlo tramite [PayPal](https://paypal.me/rgiorgiotech). Grazie di cuore! 🙌

---

## ⚠️ Disclaimer

1. **Tutto il lavoro è stato eseguito da me**, senza usare file di terze parti. Il processo ha richiesto molta ricerca. Se vuoi approfondire i dettagli tecnici, ho documentato tutto in questa repo: [ZTE-F6005-Modding](https://github.com/rgiorgiotech/ZTE-F6005-Modding).
2. **Compatibilità**: questo dump è compatibile solo con la prima versione dell'ONT F6005, non con la V3 (angoli squadrati).
3. **Non sono responsabile di alcun danneggiamento o malfunzionamento**: assicurati di capire il processo e i suoi rischi prima di procedere.

---

## 🌐 Upgrade via Web-GUI

Se l'ONT supporta l'aggiornamento firmware dalla sua interfaccia web (versioni Open Fiber) è possibile usare il file `newrootfs.img` (in questa repo) per sbloccare Telnet senza accesso alla SPI NOR flash. In questo caso è sufficiente fermarsi qui: basta caricare il file nella web-gui dell'ONT e procedere con l'update attendendo che l'ONT si riavvii.

**Attenzione**: se la pagina per l'aggiornamento software non è disponibile nella web-gui, è necessario seguire la seguente procedura per il flash via SPI.

---

## 🌐 Upgrade via Telnet

Se è già stata installata una precedente versione con Telnet sbloccato, è possibile aggiornare alla nuova versione direttamente tramite Telnet. Per questa procedura è necessario abilitare un server FTP sul proprio computer (semplice procedura disponibile per tutti gli OS, è sufficiente una ricerca su Internet).

Una volta entrati nell'ONT via Telnet, andare su `system > fs` ed eseguire il comando:
```bash
supgrade a newrootfs.img 192.168.1.2 user pass
```
dove `supgrade` è il comando superuser per l'aggiornamento; `a` è l'immagine da scrivere (nel chip ci sono due immagini, è preferibile ripetere lo stesso comando anche con `b` poiché ad alcuni OLT non piace che l'ONT abbia immagini con due versioni diverse); `newrootfs.img` è il file da utilizzare; `192.168.1.2` è l'IP del server FTP; `user` e `pass` sono le credenziali d'accesso al server FTP.

Dopo l'aggiornamento è sufficiente riavviare l'ONT.

---

## Procedura SPI Flash

### 🔧 Strumenti richiesti

#### Hardware
- CH341A SPI programmer;
- SOIC8 clip (opzionale ma consigliata per flashare senza dissaldare il chip);
- Linux (anche live ISO) o macOS - i software provati su Windows non hanno dato buoni risultati.

#### Software
- **flashrom**:
  - Su **macOS**: installabile via Homebrew package manager:
    ```bash
    brew install flashrom
    ```
  - Su **Linux**: installabile dal package manager della propria distribuzione.


### 🚀 Procedura

#### 1. Preparare l'ONT
1. **No alimentatore**: l'ONT deve essere disconnesso dalla corrente elettrica;
2. **Isolare il pin 8 (VCC)**: se viene usata una clip SOIC8, probabilmente è necessario disconnettere il pin 8 (VCC) - è l'ultimo pin, esattamente di fronte al pin 1 (che è indicato dal cavetto rosso e che corrisponde al pin col cerchietto stampato nel circuito).
3. **Backup del contenuto originale**:
   ```bash
   flashrom -p ch341a_spi -r backup.bin
   ```

#### 2. Flashare il dump modificato
1. Dal terminale, posizionarsi nella directory contenente il file `unlocked-dump.bin`.
2. Connettere il programmer CH341A al computer.
3. Eseguire il comando eseguente:
   ```bash
   flashrom -p ch341a_spi -w unlocked-dump.bin
   ```
4. Opzioni addizionali:
   - Se richiesto da flashrom, specificare il modello del chip con `-c MODEL`;
   - Aggiungere `-VVV` alla fine del comando per la  verbose (visualizzazione dei byte letti/scritti).
  
#### 3. Verifica (opzionale ma consigliata)
Dopo il flash, è possibile leggere il nuovo contenuto del chip e confrontarlo con il file del dump modificato.

1. Leggere il nuovo contenuto del chip:
   ```bash
   flashrom -p ch341a_spi -r VERIFYDUMP.bin
   ```
2. Confrontare `unlocked-dump.bin` con `VERIFYDUMP.bin`:
   ```bash
   diff unlocked-dump.bin VERIFYDUMP.bin
   ```
Se non ci sono differenze (il comando `diff` non produce output), la procedura è stata eseguita con successo.

---

Per domande o dubbi, è possibile contattarmi attraverso il mio sito [giorgiomessina.it](https://giorgiomessina.it).
