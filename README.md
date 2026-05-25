# PandaKB Sofle Choc Wireless ZMK Config

This config targets a PandaKB-branded Sofle Choc wireless RGB kit using SuperMini nRF52840 controllers with the ZMK `nice_nano_v2` board target.

## What Works

- Both halves build and flash as `nice_nano_v2` plus `sofle_left` / `sofle_right`.
- Per-key SK6812MINI-E RGB works on both halves.
- OLED support is enabled in firmware.
- A left-half ZMK Studio USB UART artifact builds successfully.

## Important Hardware Findings

- The PCB is PandaKB, and the working RGB data pin is GPIO `P0.06`.
- The LED chain length is `29` LEDs per half.
- The earlier db-ok Sofle Choc Wireless pin, GPIO `P0.08`, built successfully but did not light any LEDs on this PandaKB PCB.
- ZMK calls SK6812/WS2812 addressable LEDs `RGB_UNDERGLOW`, even when the LEDs are per-key switch-socket LEDs.
- The OLED modules should stay physically unplugged until the underside is insulated from the controller pins.

## Build Outputs

The build matrix produces:

- `sofle_left_pandakb_default.uf2`
- `sofle_left_pandakb_default_studio.uf2`
- `sofle_right_pandakb_default.uf2`

Use the non-Studio artifact first when debugging hardware. Use the Studio artifact after the normal firmware works.

## Keymap

The base, lower, and raise layers are modeled after the default Sofle layout from ZMK and the reference SofleKeyboard image.

RGB controls are on the raise layer top row:

| Key | Binding |
| --- | --- |
| `` ` `` | external power toggle |
| `1` | RGB toggle |
| `2` | RGB on |
| `3` | RGB off |
| `4` | brightness up |
| `5` | brightness down |
| `6` | next effect |
| `7` | previous effect |
| `8` | hue up |
| `9` | hue down |
| `0` | saturation up |
| far right top key | saturation down |

Bluetooth controls remain on the adjust layer, activated by holding lower and raise together.

## Flashing Notes

On macOS, copying a UF2 can show an `Input/output error` if the bootloader reboots immediately after accepting the file. If the keyboard restarts and the new firmware works, the scary copy error can be ignored.

If Finder copy fails, this often still works:

```sh
cat firmware-pandakb-default/sofle_left_pandakb_default.uf2 > /Volumes/NICENANO/FLASH.UF2
sync
```

Replace `NICENANO` with the actual bootloader volume name.

## References

- PandaKB Sofle Choc firmware branch: `PandaKBLab/zmk-for-keyboards`, branch `zmk-for-sofle-choc`
- Reference Sofle layout image: https://github.com/josefadamcik/SofleKeyboard/blob/master/Images/soflekeyboard.png
- ZMK Sofle default keymap: `zmkfirmware/zmk`, tag `v0.2`
