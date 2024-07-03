# Firmware Write Protection on ChromeOS Devices

The firmware write protect mechanism is one of the most misunderstood aspects of ChromeOS devices; many guides/blogs/how-to's incorrectly tell users to disable it as part of whatever process they are attempting to guide the user through. Hopefully this page can clear some of that up :)

## How Does Firmware Write Protection Work?

On a typical ChromeOS device, the system (or application processor/AP) firmware is stored on a [SPI](https://en.wikipedia.org/wiki/Serial_Peripheral_Interface) [flash chip](https://en.wikipedia.org/wiki/Flash_memory#Serial_flash), often in a [SOIC-8](https://en.wikipedia.org/wiki/Small_Outline_Integrated_Circuit) package. As part of the [ChromeOS security model](https://www.chromium.org/chromium-os/chromiumos-design-docs/firmware-boot-and-recovery), certain parts of the device firmware are set to be read-only. The protection of these read-only regions is implemented by a combination of hardware and software measures.

### Software write protection

The software write protection is implemented via special registers on the firmware chip. These registers allow for the software write-protect to be enabled or disabled, as well as one or more ranges of addresses to be protected / marked as read-only. This allows for parts of the chip to be protected (e.g., the RO firmware, or RO_VPD regions) and parts to be system (or user) writable (e.g., the RW_LEGACY region).

### Hardware Write Protection

The hardware write protection is an electrical circuit which prevents writing to the software protection special registers; it's normally enforced by the grounding of the !WP pin on the firmware flash chip. Thus, the hardware write protection not only protects directly these special registers, but indirectly also the data in the firmware chip.

#### HW WP Implementation

*   Early Chromebook models (2012-2013): use a jumper or switch to implement hardware write protection. All models prior to the 2013 Chromebook Pixel fall into this group.
*   Pre-CR50 models (2014-2017): use a screw to complete the ground; removing it leaves the !WP pin floating, effectively disabled. The 2013 Chromebook Pixel was the first device to use a WP screw; all Haswell, Broadwell, Baytrail, Skylake, and Braswell-based devices do as well.
*   CR50 models (2017+): On all Chromebook models with the CR50/Google Security Chip (all Kabylake/Apollolake and newer models), the !WP pin is controlled by the CR50.

On most early CR50 platforms, the CR50 sets the WP state to follow the battery sense line, so disconnecting the battery cable **from the mainboard** will disable the hardware write protect.

On some newer platforms, WP cannot be disabled by disconnecting the battery; instead there is an unpopulated jumper on the board which must be bridged.

On all CR50 devices, it is also possible to change the WP state using the [closed-case debugging (CCD)](https://chromium.googlesource.com/chromiumos/platform/ec/+/cr50_stab/docs/case_closed_debugging_gsc.md) features of the CR50, along with a special USB-C debug cable (called a SuzyQable). See the CCD section below.

## Disabling Firmware Write Protection

To fully disable the firmware write protection, one would need to:

*  Disable the hardware write protection.
*  Set the flash chip register to disable software write protection
*  Clear any protected ranges from the flash chip register (so no data will be protected; the protected range is set to start and end at 0).

::: tip NOTE
Once you disable the software write protection on your device, it will remain disabled until you manually re-enable it -- even if the hardware write protection is later re-enabled, the firmware chip contents will remain unprotected.
:::

### Disabling Hardware Write Protection

The [Supported Devices](/docs/supported-devices.md) page provides the type of write-protection used for each device, but it doesn't (yet) have the location of the WP screw for all devices which use that method. The closest thing is the [Developer Information for Chrome OS Devices](http://www.chromium.org/chromium-os/developer-information-for-chrome-os-devices) on chromium.org, which is linked for devices listed there. Eventually, I hope to have a more complete list here on this site.

For battery-powered CR50 devices, WP can be temporarily disabled by opening the device, disconnecting the battery, and booting via AC power. Devices which are not easily opened (eg, Pixelbook and Pixel Slate) or which are not battery-powered (eg, Chromeboxes) should use the [CCD method as described below](https://wiki.mrchromebox.tech/Firmware_Write_Protect.html#Disabling_WP_on_CR50_Devices_via_CCD).

### Disabling Software Write Protection

::: warning IMPORTANT
If using MrChromebox's Firmware Utility Script to flash the firmware or set the GBB flags on your device, it is not necessary (nor recommended) to manually disable the software write protection. The Firmware Utility Script will automatically disable/clear/set/enable the software write protection as required. The information below is provided purely for completeness.
:::

(read: you don't ever need to do this)

The ChromeOS/ChromiumOS version of flashrom can manipulate the software write protect register.

*   Read the status of the software write protect register:

`sudo flashrom --wp-status`

*   Disable or enable the software write protection:

`sudo flashrom --wp-disable` (or `--wp-enable`)

*   Change software write protection addresses range:

`sudo flashrom --wp-range 0 0` (`--wp-range 0,0` on newer versions of flashrom)


## Why Disable Firmware Write Protection?

There are only two real reasons to disable the firmware write protect on your ChromeOS device:

*   To change the Google Binary Block (GBB) flags
*   To flash custom firmware which modifies or overwrites the RO portions of the stock firmware

Flashing firmware which only modifies an RW portion of the firmware (like RW_LEGACY, for Legacy Boot Mode) does not require the firmware write protect to be disabled.

### GBB Flags

The GBB Flags are used to modify the boot behavior of a ChromeOS device in Developer Mode. There is a wide range of functions offered by these flags, but they are most commonly used to:

*   Shorten the Developer Mode boot screen timeout (from 30s to 1s, and remove the beep)
*   Prevent accidental disablement of Developer Mode (via spacebar)
*   Force enablement of Legacy Boot Mode (regardless of crossystem flag value)
*   Set Legacy Boot Mode as the default boot path (negates the need to use CTRL+L)

For most users, there's no need to set these flags manually, as the [Firmware Utility Script](/docs/fwscript.md) provides the functionality to set the desired timeout and default boot option, while setting the other flags to sane defaults.

::: tip NOTE
The GBB flags are a construct of the stock ChromeOS device firmware. They do not exist / cannot be set when running custom firmware which replaces the stock firmware (e.g., MrChromebox's UEFI Firmware).
:::

### Custom Firmware

Flashing custom firmware which completely replaces the stock firmware requires disabling the firmware write protect, since all RO and RW portions of the chip are overwritten.


### Disabling WP on CR50 Devices via CCD

Kabylake/Apollolake (and newer) ChromeOS devices use a custom security chip, referred to either as the Google Security Chip (GSC) or CR50 (the firmware which runs on said chip). For convenience, I'll refer to these as "CR50" devices.

In addition to providing a hardware root of trust, the CR50 provides closed-case debugging (CCD) capabilities, which allows owners additional control over their device via the use of a special USB-C debug cable (often called a Suzy-Q cable, but currently not available for purchase AFAIK). The CCD functionality of the CR50 allows us to not only disable the firmware write protect, but also to re-flash the system firmware in case something goes wrong. This process will entail:

*   Enabling Developer Mode on the device
*   Changing the CCD state from 'locked' (closed) to 'open' to allow modifications
*   Disabling the firmware WP and enabling flashing of system firmware

#### Enable Developer Mode

*   Enter Recovery Mode: press/hold ESC and Refresh, then press Power for ~1s; release all 3 keys
*   Press CTRL+D to switch to Developer Mode; confirm when prompted
*   Press CTRL+D on Developer Mode splash screen to boot in Developer Mode

On first boot, system will securely wipe all userdata (this takes a few minutes)

#### Open the CCD

*   Open a crosh shell: CTRL+ALT+T

shell

*   Check the CCD state:

sudo gsctool -a -I

The CCD state should be reported as closed

*   Open the CCD:

sudo gsctool -a -o

You will be prompted to assert physical presence (PP), which is a fancy way of saying to press the power button. Over the course of ~3 minutes, it will prompt you several times to press the power button. On the last time, the device will abruptly reboot and exit Developer Mode. Switch back to Developer Mode after this.

#### Disable WP / Enable Firmware Flashing

At this point, you will need to connect the Suzy-Q cable to your ChromeOS device in a loopback manner - both ends of the cable will be connected to it. The USB-C end needs to be in the debug port (usually the left rear port on devices with multiple USB-C ports) and be facing the correct way (the debug part of the cable is not reversible). The USB-A end of the cable can connect to any open port. On devices with only USB-C ports, an adapter must be used.

*   Verify cable connection:

ls /dev/ttyUSB\*

This command should return 3 items: ttyUSB0, ttyUSB1, and ttyUSB2

If not, then your cable is connected to the wrong port or is upside down

*   Change to a root shell:

sudo bash 

*   Disable hardware write-protect:

echo "wp false" > /dev/ttyUSB0

echo "wp false atboot" > /dev/ttyUSB0

*   Enable all CCD functionality always (allows unbricking/recovery in case CCD state is reset):

echo "ccd reset factory" > /dev/ttyUSB0

*   Verify changes:

gsctool -a -I

The CCD state should be opened, and the current value for all CCD flags should be set to Y/Always.  
Note: the ccd command shows the current value followed by the default value in parentheses, so you can ignore the latter.

crossystem wpsw_cur

The current WP value should be 0

At this point, you can now make any changes to the GBB Flags or flash custom firmware. Remember to make a backup when prompted by the script!

**IMPORTANT**: If you plan on flashing UEFI firmware via the Firmware Utility Script, you must reboot after running the CCD commands / before running the script, otherwise you will be unable to flash the firmware.
