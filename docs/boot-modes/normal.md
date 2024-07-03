# Normal/Verified Boot Mode

In Normal/Verified Boot Mode, the read-only (RO) part of the firmware verifies the read-write (RW, aka updateable) part of the firmware, then executes it. The RW firmware verifies all other firmware is up-to-date (EC, power delivery, touchpad, touchscreen, storage, etc), verifies the (active) ChromeOS kernel on the internal storage, then boots the OS.

*   Can only boot Google-signed ChromeOS images
*   Full verification of firmware and OS kernel
*   No root (sudo) access to the system, no ability to boot Linux natively (aka bare metal)
*   Automatically boots to Recovery Mode if any step of Verified Boot fails
*   This is the default / out-of-the-box configuration for all ChromeOS devices
