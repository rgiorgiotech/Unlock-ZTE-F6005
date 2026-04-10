# Unlocking Telnet on ZTE F6005 ONT (First Version)

This guide illustrates how to unlock Telnet on the ZTE F6005 V6 ONT (first version, rounded corners). A modified dump (`unlocked-dump.bin`) will be flashed directly onto the device's SPI chip.

The `newrootfs.img` file can be used for direct updates via the web interface or, if the device is already unlocked, via Telnet (see relevant sections).

If this project helped you and you'd like to buy me a coffee ☕️ or a pizza 🍕, you can do so via [PayPal](https://paypal.me/rgiorgiotech). Thank you so much! 🙌

---

## ⚠️ Disclaimer

1. **All work was performed by me**, without using third-party files. The process required extensive research. For further technical details, I have documented everything in this repo: [ZTE-F6005-Modding](https://github.com/rgiorgiotech/ZTE-F6005-Modding).
2. **Compatibility**: This dump is only compatible with the first version of the F6005 ONT, not the V3 (squared corners).
3. **I am not responsible for any damage or malfunction**: Ensure you fully understand the process and its risks before proceeding.

---

## 🌐 Upgrade via Web-GUI

If your ONT supports firmware updates through its web interface (Open Fiber versions), you can use the `newrootfs.img` file (included in this repo) to unlock Telnet without needing SPI NOR flash access. In this case, you can stop here: simply upload the file to the ONT's web-gui and proceed with the update, then wait for the ONT to reboot.

**Warning**: If the software update page is not available in the web-gui, you must follow the SPI flash procedure below.

---

## 🌐 Upgrade via Telnet

If a previous version with Telnet already unlocked has been installed, you can update to the new version directly via Telnet. For this procedure, you need to enable an FTP server on your computer (a simple process available for all operating systems; a quick web search will suffice).

Once you have accessed the ONT via Telnet, navigate to `system > fs` and execute the following command:
```bash
supgrade a newrootfs.img 192.168.1.2 user pass
```
Where `supgrade` is the superuser command for updates; `a` is the image to be written (the chip contains two images; it is recommended to repeat the command with `b` as some OLTs do not tolerate the ONT having two different image versions); `newrootfs.img` is the file to be used; `192.168.1.2` is your FTP server's IP address; `user` and `pass` are your FTP server credentials.

After the update, simply reboot the ONT.

---

## SPI Flash Procedure

### 🔧 Requirements

#### Hardware
- CH341A SPI programmer;
- SOIC8 clip (optional but recommended for flashing without desoldering the chip);
- Linux (including live ISOs) or macOS — software tested on Windows did not yield reliable results.

#### Software
- **flashrom**:
  - On **macOS**: Install via Homebrew package manager:
    ```bash
    brew install flashrom
    ```
  - On **Linux**: Install via your distribution's package manager.


### 🚀 Procedure

#### 1. Prepare the ONT
1. **No power supply**: The ONT must be disconnected from the power source.
2. **Isolate pin 8 (VCC)**: If using a SOIC8 clip, you will likely need to disconnect pin 8 (VCC) — it is the last pin, directly opposite pin 1 (indicated by the red wire, corresponding to the pin with the dot printed on the circuit).
3. **Original content backup**:
   ```bash
   flashrom -p ch341a_spi -r backup.bin
   ```

#### 2. Flash the modified dump
1. From the terminal, navigate to the directory containing the `unlocked-dump.bin` file.
2. Connect the CH341A programmer to your computer.
3. Execute the following command:
   ```bash
   flashrom -p ch341a_spi -w unlocked-dump.bin
   ```
4. Additional options:
   - If prompted by flashrom, specify the chip model using `-c MODEL`.
   - Add `-VVV` at the end of the command for verbose mode (to view the bytes being read/written).
  
#### 3. Verification (optional but recommended)
After flashing, you can read the new chip content and compare it with the modified dump file.

1. Read the new chip content:
   ```bash
   flashrom -p ch341a_spi -r VERIFYDUMP.bin
   ```
2. Compare `unlocked-dump.bin` with `VERIFYDUMP.bin`:
   ```bash
   diff unlocked-dump.bin VERIFYDUMP.bin
   ```
If there are no differences (the `diff` command produces no output), the procedure was successful.

---

For questions or concerns, feel free to contact me through my website [giorgiomessina.eu](https://giorgiomessina.eu).
