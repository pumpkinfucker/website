# Manually Flashing Firmware

::: danger
Flashing your own firmware has the potential to brick your device. Do not do this unless you are sure you know what you're doing **and have a way to recover from a bad flash**. Some level of knowledge with using the Linux command line is required.
:::

The steps below assume you are flashing an image named `coreboot.rom`; substitute it as necessary.

1. Download flashrom, decompress, and ensure it is executable:
   * `wget -o flashrom.tar.gz https://mrchromebox.tech/files/util/flashrom-libpci38.tar.gz`
   * `tar zxf flashrom.tar.gz`
   * `chmod +x flashrom`
2. Flash your custom ROM
   * Backup your current firmware (just in case things go wrong):
     `sudo ./flashrom -p internal -r backup.rom`
   * Flash your custom firmware: 
       * AMD devices: `sudo ./flashrom -p internal -w coreboot.rom`
       * Intel devices: `sudo ./flashrom -p internal --ifd -i bios -w coreboot.rom`
3. Reboot
   * Assuming flashrom shows `success` at the end of the process, reboot.

::: warning IMPORTANT
Flashing your firmware directly in this manner does not preserve any of the device-specific info from the stock firmware (e.g., serial number, HWID, ethernet MAC address) that flashing via the Firmware Utility Script does. 
:::
