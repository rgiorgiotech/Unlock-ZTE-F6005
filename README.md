# Unlocking Telnet on ZTE F6005 ONT (first version)

This guide explains how to unlock Telnet on ZTE F6005 ONT (first version, with rounded corners). A modified dump (`UNLOCKEDTELNET.bin`) will be flashed directly onto the SPI NOR Flash of the device.

The `rootfs.img` file contains the extracted firmware from the modified dump and can be used for direct firmware upgrade via the web interface (see the *Upgrade via Web-GUI* section).

---

## ⚠️ Disclaimer

1. **This modification was created entirely by me**, without using any third-party files. The process required extensive research. If you want to understand the technical details, I've documented everything in this repo: [ZTE-F6005-Modding](https://github.com/rgiorgiotech/ZTE-F6005-Modding).
2. **Compatibility**: this dump is only compatible with first version of ZTE F6005 ONT, not with the _v3_ one.
3. **I am not responsible for any damage or malfunction caused by following this procedure**: ensure you understand the process and its risks before proceeding.

---

## 🔧 Required Tools

### Hardware
- CH341A SPI programmer;
- SOIC8 clip (optional, useful for flashing without desoldering);
- A computer running Linux (Live ISO is fine) or macOS - all the software tried on Windows didn't flash correctly in my tests.

### Software
- **flashrom**:
  - On **macOS**: Install via Homebrew package manager:
    ```bash
    brew install flashrom
    ```
  - On **Linux**: Install via your distribution's package manager.

---

## 🚀 Procedure

### 1. Preparing the ONT
1. **No power supply**: the ONT must be disconnected from the mains;
2. **Isolate pin 8 (VCC)**: if using an SOIC8 clip for reading/writing, you may need to disconnect pin 8 (VCC) - this is the last pin counterclockwise from pin 1 (this one usually marked with a dot);
3. **Backup the original flash**:
   ```bash
   flashrom -p ch341a_spi -r backup.bin
   ```

### 2. Flashing the Modified Dump
1. Move to the directory containing the `UNLOCKEDTELNET.bin` file.
2. Connect the CH341A programmer to your computer.
3. Execute the following command:
   ```bash
   flashrom -p ch341a_spi -w UNLOCKEDTELNET.bin
   ```
4. Additional options:
   - If required by flashrom, specify the chip model with `-c MODEL`;
   - Add `-VVV` for verbose output.
  
### 3. Verification (optional but recommended)
After flashing, you can read back the flash content and compare it with the modified dump to ensure the procedure was successful. By default, flashrom performs an integrity check after writing.

1. Read back the flash content:
   ```bash
   flashrom -p ch341a_spi -r VERIFYDUMP.bin
   ```
2. Compare `UNLOCKEDTELNET.bin` with `VERIFYDUMP.bin`:
   ```bash
   diff UNLOCKEDTELNET.bin VERIFYDUMP.bin
   ```
If there are no differences (i.e., `diff` produces no output), the flash process was successful.

---

## Upgrade via Web-GUI

If the ONT supports firmware upgrades via its web interface (i.e., Open Fiber version), you can try flashing `rootfs.img` (available in this repository) to unlock Telnet without requiring SPI NOR Flash access.

**Warning**: This method has not been extensively tested. If the software upgrade page is not available in your ONT’s web interface, you must follow the SPI flashing procedure instead.

---

## Acknowledgments
Special thanks to <ins>Skizzo</ins> for assistance in modding, [FedeBertos](https://github.com/FedeBertos) and <ins>Axtermax</ins> for successfully testing this procedure.

---

For any questions or issues, feel free to open an issue in this repository.
