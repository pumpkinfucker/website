## Developer Mode

Developer Mode relaxes the security restrictions of Normal/Verified Boot Mode, allowing for more control over the device.

*   “Jailbreak” mode built-in to every ChromeOS device
*   Loosened security restrictions, allows root/shell access, ability to modify ChromeOS filesystem and/or firmware
*   Verified Boot (signature checking) disabled by default, but can be re-enabled
*   Enabled via \[CTRL+D\] on the Recovery Mode boot screen (older devices) or the Advanced Options menu item (newer devices)
*   Boots to the developer mode boot screen (on older devices, the white screen with ‘OS verification is off’ text shown above), from which the user can select via keystroke where to boot:
    *   ChromeOS (in developer mode) on internal storage ( \[CTRL+D\] )
    *   ChromeOS/ChromiumOS on USB ( \[CTRL+U\] )
    *   Legacy Boot Mode (older devices) / Alternative Bootloader (newer devices) ( \[CTRL+L\] )
*   Boot screen displays the ChromeOS device/board name in the hardware ID string (eg, `PANTHER F5U-C92`, which is useful to know in the context of device recovery, firmware support, or in determining what steps are required to install a given alternate OS on the device.


### Developer Mode Basics

Putting your ChromeOS device into [Developer Mode](https://www.chromium.org/chromium-os/chromiumos-design-docs/developer-mode) relaxes some of the restrictions in Verified Boot Mode, and gives the user a bit more control over the system:

*   Provides full root access to the system via a VT2 (virtual terminal) shell
*   Allows modification of the ChromeOS kernel and root filesystems
*   Allows for booting alternate OSes via Legacy Boot Mode/Alternative Bootloader Menu on supported systems
*   Allows for serveral types of firmware updates/upgrades (some of which require disabling the firmware write-protect first)

Because Developer Mode is inherently less secure than Verified Boot Mode, as a warning when booting you will be greeted by the developer mode boot screen:

[<p align="center"> <img src="/images/cros_dev_mode.jpg" width="500"/></p>](/images/cros_dev_mode.jpg)

The Developer Mode boot screen will show for 30 seconds, then beep and boot into ChromeOS (by default). You can bypass this timeout by pressing \[CTRL+D\] to boot into ChromeOS, or \[CTRL+L\] to boot in legacy mode/to the Alternate Bootloader Menu if available. The length of the timeout (2s or 30s) and the default boot target (ChromeOS or Legacy Boot Mode) can be changed by setting the firmware boot flags (aka GBB flags).

**Note:** the easiest way to set the firmware boot flags is via the [ChromeOS Firmware Utility Script](docs/fwscript); it presents users with the option to set the boot timeout and boot target without having to know which flags to set / what values to use.

This script function also sets the flags to force-enable Developer Mode (so you don't accidentially exit it) and force enable Legacy Boot Mode (so you battery dying doesn't reset the crossystem boot flag). Setting the firmware boot flags / GBB flags requires the firmware write-protect to be disabled, since it's modifying a normally-read-only section of the firmware (the GBB region).

### Enabling Developer Mode

Entering Developer Mode requires you to first boot into Recovery Mode. For Chromebooks, this means pressing \[ESC+Refresh+Power\]; On Chromeboxes, there is usually a physical recovery button (often a small hole above the SD card reader) which must be depressed when powering on. Once at the recovery screen, press \[CTRL+D\] to enable developer mode, then confirm when prompted. As a security measure, transitioning to/from Developer Mode will wipe out all ChromeOS user data, essentially powerwashing (resetting) the device.

Exiting Developer Mode is as simple as following the instructions on the Developer Mode boot screen (usually pressing \[SPACE\]), but may require resetting the firmware boot flags if you've changed them. As with entering developer mode, exiting will wipe all ChromeOS user data, so if you plan on keeping your device in developer mode, it's a good idea to set the firmware boot flags to prevent accidental exiting and loss of data.

### Getting a (Root-capable) Shell

Once in developer mode, there are two ways to get a root-capable shell, which is required for doing anything useful:

*   On the login screen, press \[CTRL+ALT+F2\] (F2 is right-arrow on ChromeOS keyboards), then login with user 'chronos' (no password is required, nor should one be set)
*   When logged in (as a guest is ok) and with a browser window open, press \[CTRL+ALT+T\], then type `shell` and press \[ENTER\]

Once you have a root-capable shell, you can do things like install Linux (via [crouton](https://github.com/dnschneid/crouton) or [chrx](https://chrx.org)), update the firmware, etc.
