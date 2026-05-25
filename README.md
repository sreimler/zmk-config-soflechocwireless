# PandaKB Sofle Choc Wireless ZMK Config

This is the working ZMK config for a PandaKB-branded AliExpress Sofle Choc wireless RGB kit using SuperMini nRF52840 controllers with the ZMK `nice_nano_v2` board target.

## ✅ Current Known-Good State

- RGB works on both halves.
- All installed switch-socket LEDs have been confirmed working.
- OLED firmware support is enabled, but the OLED modules should stay physically unplugged until the underside is insulated.
- The left-half ZMK Studio USB UART artifact builds successfully.
- The repo is intentionally pinned to ZMK `v0.2`.

## 🧠 Hardware Facts To Remember

- The PCB says PandaKB.
- The working RGB data pin is GPIO `P0.06`.
- The working LED chain length is `29` LEDs per half.
- The earlier db-ok Sofle Choc Wireless pin, GPIO `P0.08`, builds successfully but does not light LEDs on this PandaKB PCB.
- ZMK calls SK6812/WS2812-style addressable LEDs `RGB_UNDERGLOW`, even when the LEDs are per-key switch-socket LEDs.
- The LEDs are SK6812MINI-E.
- The OLED modules are SSD1306-style I2C displays.
- The OLED underside can contact controller pins. Add Kapton/polyimide insulation before installing a display.

## 🧩 Files That Matter

- `config/sofle.keymap`: layers, key behavior, RGB controls, encoder behavior, and the temporary RGB devicetree block.
- `config/sofle.conf`: ZMK feature flags for display, encoders, RGB, and Studio.
- `config/west.yml`: pins ZMK to `v0.2`.
- `build.yaml`: defines the GitHub Actions build matrix.
- `.github/workflows/build.yml`: uses the ZMK `v0.2` user config workflow.

## 🛠️ Build Outputs

The build matrix produces:

- `sofle_left_pandakb_default.uf2`
- `sofle_left_pandakb_default_studio.uf2`
- `sofle_right_pandakb_default.uf2`

Use the non-Studio artifact first when debugging hardware. Use the Studio artifact after the normal firmware works.

## ⌨️ Keymap Shape

The keymap is customized for macOS:

- Bottom-row Ctrl and Cmd/GUI are swapped from the ZMK Sofle default.
- Tab and Esc are swapped from the ZMK Sofle default.
- Raise has macOS navigation, media, volume, and screen brightness controls.
- RGB and Bluetooth controls are on adjust.
- Lower plus raise activates adjust through the conditional layer.
- The physical Enter thumb key on raise toggles adjust on with `&tog 3`.
- The same physical key on adjust toggles adjust back off with `&tog 3`.

## 🖼️ Keymap Diagram

The current layers are rendered from `config/sofle.keymap` with `keymap-drawer`:

![Current Sofle keymap](keymap-drawer/sofle.svg)

Generated files:

- `keymap-drawer/sofle.yaml`: parsed keymap data.
- `keymap-drawer/sofle.svg`: rendered layer diagram used above.
- `keymap-drawer/sofle-layout.dtsi`: local copy of the ZMK `v0.2` Sofle physical layout used by the renderer.

To regenerate locally:

```sh
keymap parse -c 12 -z config/sofle.keymap -o keymap-drawer/sofle.yaml
keymap draw -d keymap-drawer/sofle-layout.dtsi -l josefadamcik_sofle_layout keymap-drawer/sofle.yaml -o keymap-drawer/sofle.svg
```

## 🌈 RGB Controls

RGB controls live on adjust, not raise. Hold lower plus raise to reach adjust, or use raise plus the physical Enter thumb key to latch adjust on. Press the same Enter thumb key again on adjust to latch it off.

| Key on Adjust | Binding |
| --- | --- |
| `` ` `` | External power toggle |
| `1` | Hue down |
| `2` | Hue up |
| `3` | Saturation down |
| `4` | Saturation up |
| `Q` | Brightness down |
| `W` | Brightness up |
| `D` | RGB off |
| `F` | RGB toggle |
| `G` | RGB on |
| `Z` | Previous effect |
| `X` | Next effect |
| `C` | Animation speed down |
| `V` | Animation speed up |
| left inner thumb | Toggle adjust layer off |

## 🔌 External Power Toggle

`&ext_power EP_TOG` is kept on adjust as a troubleshooting/control key.

It does not need a prime spot on raise right now. RGB turns on through the normal `&rgb_ug` controls on this build, and the physical battery switch still handles hardware battery power. The raise-layer top-left key is transparent for now so it can be reassigned later.

## 📡 Bluetooth Controls

Bluetooth controls are on the adjust top row:

- `` ` `` position: `BT_CLR`
- `1` through `5`: `BT_SEL 0` through `BT_SEL 4`

## ✏️ Editing The Keymap

Recommended editor:

- https://nickcoutsos.github.io/keymap-editor/

That editor can open this GitHub repo and commit keymap changes back to the branch. After changing the keymap, GitHub Actions must rebuild the firmware, and the resulting UF2 must be flashed again.

Useful ZMK keycode reference:

- https://zmk.dev/docs/keymaps/list-of-keycodes

Keymap diagram tool:

- https://github.com/caksoylar/keymap-drawer
- https://caksoylar.github.io/keymap-drawer/

Notes:

- ZMK uses `GUI` for Cmd on macOS.
- ZMK keycodes are spelling-sensitive. A tiny shorthand typo can cause an opaque build error.
- Consumer/media keys often use `C_` names, for example `C_VOLUME_UP`.
- Keyboard page keys often use plain names, for example `LEFT`, `RIGHT`, `PG_UP`, `DEL`.

## ⚡ Flashing Notes

On macOS, copying a UF2 can show an `Input/output error` if the bootloader reboots immediately after accepting the file. If the keyboard restarts and the new firmware works, that scary copy error can be ignored.

If Finder copy fails, this often still works:

```sh
cat firmware-pandakb-default/sofle_left_pandakb_default.uf2 > /Volumes/NICENANO/FLASH.UF2
sync
```

Replace `NICENANO` with the actual bootloader volume name.

## 🧪 Safe Bring-Up Order

1. Flash the non-Studio left firmware first.
2. Confirm normal keys work.
3. Confirm RGB works through adjust.
4. Flash the right half once its hardware is ready.
5. Try the Studio artifact only after the normal firmware works.
6. Add OLED hardware only after insulation is installed.

## 🧯 Things That Look Weird But Are Expected

- `RGB_UNDERGLOW` means addressable RGB in ZMK, not necessarily dedicated underglow LEDs.
- `chain-length = <29>` is correct for this PandaKB PCB.
- `P0.06` is correct for this PandaKB PCB.
- `P0.08` was the wrong RGB data pin for this PCB, even though it appears in other Sofle Choc Wireless references.
- OLED and RGB can be enabled in the same firmware build, but OLED hardware should not be installed until the underside is insulated.
- ZMK Studio can edit some behavior live, but repo/keymap changes still need a GitHub Actions rebuild and reflash.

## 🔗 References

- PandaKB Sofle Choc firmware branch: `PandaKBLab/zmk-for-keyboards`, branch `zmk-for-sofle-choc`
- Reference Sofle layout image: https://github.com/josefadamcik/SofleKeyboard/blob/master/Images/soflekeyboard.png
- ZMK Sofle default keymap: `zmkfirmware/zmk`, tag `v0.2`
- ZMK keycode list: https://zmk.dev/docs/keymaps/list-of-keycodes
- Keymap Editor: https://nickcoutsos.github.io/keymap-editor/
