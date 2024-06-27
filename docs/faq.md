 

## Frequently Asked Questions

#### Q: I want to buy a Chromebook to run Windows or Linux -- which should I buy?

**A:** Buying any Chromebook with the intention of running Windows or Linux is not a great idea. Many can't boot anything other than ChromeOS; Those that can boot Linux (or Windows) often have functional deficiencies -- DO NOT EXPECT EVERYTHING TO WORK OUT OF THE BOX. Older models may fair better compatibility wise, but there are still lots of caveats, and it's not recommended to buy a Chromebook as a cheap Linux device.

That said, [the chrultrabook project's documentation](https://docs.chrultrabook.com/docs/firmware/supported-devices.html) provides the most up-to-date info on Linux/Windows compatibility.  
For Windows, [coolstar's site](https://coolstar.org/chromebook/windows.html) also provides compatibility info and driver downloads.

#### Q: I've installed Windows or Linux, and now something doesn't work?

**A:** Check the [chrultrabook docs](https://docs.chrultrabook.com) for known issues/workarounds, and go to the [chrultrabook forums](https://forum.chrultrabook.com/) for help.

#### Q: How can I get audio working under Linux?

**A:** See [the post-install notes on the Chrultrabook docs](https://docs.chrultrabook.com/docs/installing/installing-linux.html#fixing-audio)

#### Q: I just want to boot Linux from USB on my Chromebook, what do I need to do?

**A:** Check the Supported Devices page, to ensure your device has functional RW\_LEGACY firmware available. If so, then simply put your device into Developer Mode, then run my script and update the RW\_LEGACY firmware (no need to disable the firmware write protect for this). Reboot, press CTRL+L to boot in Legacy Boot Mode/Alternative Bootloader Menu, then press ESC and select your USB from the boot menu.

#### Q: I just installed the UEFI firmware, and now my device boots to a black screen that says 'shell' - what do I do?

**A:** You're in the EFI shell; the firmware boots there when it can't find a valid UEFI boot device, either externally (USB, SD) or internally. Just type 'exit' to get back to the the UEFI settings menu, from which you can select your boot device or reboot.  
  
You need to install a UEFI-boot capable OS from a properly formatted USB/SD device. If you're creating the install media from Windows, use [Rufus](https://rufus.akeo.ie) to write the ISO to USB.  
If writing a Windows ISO, select the ISO, then set the Partition scheme to 'MBR for UEFI' or 'GPT for UEFI;' 'MBR for UEFI-CSM' will not work since CSM = Legacy BIOS and the UEFI firmware doesn't support Legacy mode.  
For a Linux ISO, select the ISO, then change the write mode from 'ISO Image' to 'DD Image' from the drop-down menu. As most Linux ISO images these days are compatible with both Legacy and UEFI booting, this ensures maximum compatibility.

#### Q: Should I install 32 or 64-bit OS?

**A:** The UEFI Full ROM firmware only supports 64-bit OS, so be sure to use the 'x86\_64' version of whatever OS you want to install. USB media created from 32-bit ISOs will simply fail to boot.

#### Q: I'm using your UEFI firmware, installed my OS \[Linux\], and it still boots to the EFI shell - what do I do?

**A:** Sounds like your Linux distro doesn't install the EFI bootloader in the default location - no worries, it's an easy fix:  
  
Type 'exit' to return to the UEFI settings menu, then select Boot Maintenance Manager. From there, select Boot From File, then navigate to and boot from /EFI/\[distro name\]/grubx64.efi (where \[distro name\] will be ubuntu, arch, debian, etc). Once your OS is booted, open a terminal/shell, and type the following (observing case):  
  
sudo su  
mkdir -p /boot/efi/EFI/BOOT  
cp /boot/efi/EFI/\[distro name from above\]/grubx64.efi /boot/efi/EFI/BOOT/BOOTX64.efi  
  
then reboot to test. What we're doing is copying the grub EFI boot stub from the OS installed location to the location the firmware is expecting (/EFI/BOOT/BOOTX64.efi) on the EFI system partition (ESP), which most (Debian/Ubuntu-based?) distros will mount at /boot/efi. You may need to adjust slightly for your distro, but these instructions should work in most cases.

#### Q: I'm running Legacy boot firmware now - can I switch to the UEFI firmware?

**A:** You can, but your existing OS install won't boot. You'll need to either reinstall the OS (often the easiest course of action), or you can attempt to migrate your existing install, but this isn't ideal and should be considered unsupported.

#### Q: I accidentally updated to the UEFI firmware, and now my existing OS won't boot - what do I do?

**A:** Two options:  
  1) Reinstall your OS  
  2) Boot a UEFI-capable ISO from USB, then re-run the Firmware Utility Script. From the main menu, choose the unlock option (U), then you can go back to your previous firmware, by one of the following:  
     a) restore from a backup file  
     b) choose the option to flash the Full ROM, then choose Legacy Boot (L) when prompted  
     c) restore the stock firmware, then re-flash either the RW\_LEGACY or BOOT\_STUB firmware, as applicable for your device  
  NOTE: re-flashing the Legacy Full ROM option is only avialable to a small subset of devices - be sure to consult the [Supported Devices page](https://mrchromebox.tech/#devices) to be sure it's an option for your device.

#### Q: The Windows installer says none of my partitions are large enough to install Windows - halp?

**A:** When installing Windows on the internal storage of a ChromeOS device (which is usually 16/32GB at best), you need to first delete all the existing (ChromeOS) partitions -- even the EFI system partition -- and then choose to install Windows into the unpartitioned space.

#### Q: I flashed the firmware and need to install an OS, but my USB drive isn't detected/isn't booting - what now?

**A:** If you are having problems with USB drives not detecting:  
    Make sure you created the USB install media correctly:  

*   Be sure to use a 64-bit OS; on the Windows side, use only Windows 10 -- Windows 7 will not work, Win 8/8,1 doesn't support all the required drivers; 32-bit anything (Windows or Linux) will not work (the UEFI firmware is 64-bit only)
*   For Windows, use Rufus or the Windows Media Creation Tool. Almost any options in Rufus should work, but UEFI/GPT is recommended. FAT vs NTFS should not matter. The UEFI firmware supports both
*   For Linux, ensure your distro supports UEFI. Any tool that supports UEFI Linux (such as Rufus) or a dd mode or dd itself on any \*nix should be able to make a working installation USB. Do **not** use Unetbootin. It is known to have issues making UEFI-capable installation USBs.  
    
*   On Chromeboxes, the rear ports are more reliable, so use those. Also, sometimes USB keyboards can interfere with detection of USB media, so try booting without the keyboard connected.  
    
*   Any blogs, guides, or instructions that tell you to enable Legacy/CSM mode as part of installation are not relevant for ChromeOS hardware and should be discarded completely. The UEFI firmware does not support Legacy/CSM mode. All new PCs, even if they have a Legacy/CSM mode, ship with UEFI as the default. Windows 8+ has good UEFI support. Linux has had EFI/UEFI support for over a decade. macOS has never supported Legacy BIOS. Common \*BSD variants either have UEFI support or are in the process of adding it. You should not need any Legacy mode

    Some drives are slow and the firmware does not detect them in one go:  

*   The EFI shell is your friend: just type "exit" to go to the GUI menu
*   If you can't get a drive to detect at power on, try plugging it in on the shell page, then exiting the shell with "exit" and see if it appears under "Boot Menu"
*   If not, got to "Boot Manager" and, with the USB drive still plugged in, pick "Reset System." This will reset with the USB drive still partly powered on and will increase the odds of proper detection
*   You might also try putting a powered hub between the USB drive and ChromeOS device. That can help improve the odds of proper detection if the drive is not powering on in time
*   USB drive still not detecting? Try another drive. Also, some people have reported higher success rates with SD cards

#### Q: UEFI is cool and all, but I'd like to revert to ChromeOS. How can I do that?

**A:** No problem, you just need to restore the stock firmware first:  

*   Boot a 64-bit Linux Live USB (Ubuntu, Fedora, eg)
*   Run the Firmware Utility Script (on Ubuntu, may need to install curl first: sudo apt install curl)
*   Select the option to restore the stock firmware, follow the prompts and reboot when complete
*   Insert Recovery media when prompted
*   After Recovery complete, run Firmware Utility Script again, and reset the firmware boot flags to default
*   (Optional) Reboot, and exit Developer Mode

#### Q: I'm trying to exit Developer Mode, but when I press space to exit it says:  
"WARNING: TONORM prohibited by GBB\_FORCE\_DEV\_SWITCH\_ON" - how to I fix this?

**A:** The 'Set Boot Options (GBB Flags)' option of the Firmware Utility Script automatically sets the GBB Flag to force Developer Mode on when a non-default option is selected, to mitigate the problem of accidentally exiting Developer Mode and wiping your ChromeOS user data. To clear the GBB flags / reset them to factor default, simply re-run the Firmware Utility Script and choose the option to reset the GBB Flags to factory default.  You can also do this from a ChromeOS terminal with the following command: sudo /usr/share/vboot/bin/set\_gbb\_flags.sh 0x0 and then reboot

#### Q: I'm trying to install Cloudready/ChromeOS Flex and use in a managed environment, but getting an error related to the TPM - how to I fix this?

**A:** The MrChromebox-4.20.0 firmware release added support for TPM management via the UEFI Device Manager, so this should no lonmger be an issue. If running an older firmware version, boot a current Linux ISO and run the Firmware Utility Script to update to the latest release.

#### Q: I am trying to boot Linux from USB and getting an error:  
"error: unknown filesystem. alloc magic is broken at 0x78cd97c0" -- how do I fix it?

**A:** Your ISO wasn't written to USB properly -- as an image, not an ISO -- and you need to redo it using the proper tool/settings. Etcher or Rufus in dd mode are the recommended options. The ChromeOS Recovery tool also works properly.

#### Q: The Firmware Utility script fails to run under Ubuntu 22.04 (or derivatives) with a file not found error - how to fix?

**A:** The Snap-installed curl app in Ubuntu 22.04 prevents the script from working properly; remove it and install curl via apt instead.