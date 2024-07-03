# Known Issues

## Devices

::: tip NOTE
You should probably avoid buying any devices with severe issues, and consider selling if you have one.
:::

### Severe Issues

* Samsung Chromebook 3 (CELES): Numerous hardware bugs that make it difficult to use.
* Samsung Galaxy Chromebook (KOHAKU): Badly designed hardware, deteriorating trackpad grounding, possibly faulty backlight circuit.

### Minor Issues

* Samsung Chromebox Series 3 (STUMPY): DisplayPort output is non-functional under Windows, and VGA output through the DVI port is non-functional.

## Firmware

The issues listed below are longstanding and unlikely to change. The Github [Firmware Issue Tracker](https://github.com/MrChromebox/firmware/issues/)


* **All ARM-based  Chromebooks**: The MrChromebox project does not support these devices at all.

* GeminiLake: Legacy boot mode/alternative firmware menu works but does not show payload list

* AMD Stoneyridge: Most Stoney Ridge devices do not currently have functional Legacy Boot Mode due to bugs in Google's firmware.

* AMD Cezanne: Legacy Boot Mode/Alternative Firmware menu works, edk2 boots, but screen backlight is off. Will hopefully be fixed in next Google fw update (fingers crossed).

::: tip NOTE
For known issues relating to OS support, see the [chrultrabook documentation](https://docs.chrultrabook.com/docs/installing/known-issues.md).
:::
