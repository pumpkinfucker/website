## Recovery Mode

A ChromeOS device boots to Recovery Mode when something goes wrong. It’s also how you switch from Normal Mode to Developer Mode.

Some example recovery mode screens: 

<p align="center">
<img src="/images/cros_recovery_old.jpg" width="300">
<img src="/images/cros_recovery_old_details.jpg" width="300"/>
</p>
<p align="center">
<img src="/images/cros_recovery_new.jpg" width="300"/>
<img src="/images/cros_recovery_new_details.jpg" width="300"/>
</p>

*   User presented with Recovery Mode boot screen (on older devices, this is a white screen with ‘ChromeOS is missing or damaged’ text)
    *   Pressing \[tab\] shows the recovery reason, along with other firmware-related info
        *   On older devices, in a white-on-black text block in the upper left of the screen
        *   On newer devices, in the center of the screen
*   Boots only external media (USB/SD) with a Google-signed ChromeOS recovery image
*   Is booted to automatically when the Verified Boot process fails
*   Can be manually invoked:
    *   On Chromebooks, via keystroke: press \[ESC+Refresh+Power\] simultaneously, then release  
        (only works with the built-in keyboard, for security reasons)
    *   On Chromeboxes, by pressing/holding a physical recovery button (usually with a paperclip) at power-on
    *   On Convertibles/Tablets, by pressing/holding the \[Power\], \[Vol+\], and \[Vol-\] buttons for 10s and then releasing
*   Allows for transition from Verified Boot Mode to Developer Mode:
    *   On Chromebooks/Chromeboxes, via keystroke: \[CTRL+D\]
    *   On Convertibles/Tablets, via button press: Vol+/Vol- simultaneously
*   Using ChromeOS [recovery media](https://support.google.com/chromebook/answer/1080595?hl=en) on USB/SD will completely re-partition/reformat the device’s internal storage and reload ChromeOS (as well as some RW firmware components – including RW\_LEGACY)

**Note:** The ChromeOS recovery process does **not** reset the firmware boot flags (GBB Flags), so if those are changed from the default, they will still need to be reset to the factory default setting post-recovery.