# Hillside ZMK firmware

![hillside](https://imgur.com/emWDXiT.png)
[![Build](https://github.com/mmccoyd/zmk-config/actions/workflows/build.yml/badge.svg)](https://github.com/mmccoyd/zmk-config/actions/workflows/build.yml)

This is the [ZMK](https://zmk.dev/docs) firmware
 for the [Hillside](https://github.com/mmccoyd/hillside) family of split ergonomic keyboards.

It contains keymap definition files for three boards in [./config](./config):

 - Hillside 52 with 3x6+3+5 keys
 - Hillside 48 with 3x6+1+5 keys
 - Hillside 46 with 3x6+5 keys

Pushing changes will build all the keyboards. You need to be signed in to a GitHub account to push changes and build the firmware. To not waste build time, comment out the keyboards in [./build.yaml](./build.yaml) that you do not have.

To build the firmware:

- Fork this repo on GitHub
- Clone your fork locally
- Trigger a build:
  - Make a trivial change to ./build.yaml (or any non *.md file)
  - Push that change
- Look in the [Actions](https://github.com/mmccoyd/zmk-config/actions) tab
     for the build triggered by that change. 
- Wait for the build to finish
- Click on the build link next to the green checkbox
- Download the artifact file with the firmware
- See [Installing The Firmware](https://zmk.dev/docs/user-setup#installing-the-firmware)
  for more details from there.

*Once* your board works with the default firmware,
  you can modify the keymap.
Your copies of the default Hillside keymaps are in:

- [./config/hillside52.keymap](./config/hillside52.keymap)
- [./config/hillside48.keymap](./config/hillside48.keymap)
- [./config/hillside46.keymap](./config/hillside46.keymap)

Modify those as needed. Pushing the change will trigger a build as above.

If you want to enable features,
  modify the appropriate ./config/hillside*.conf file.

To add RGB support, uncomment the lines in the ./config/hillside*.conf file
  and add the ```&rgb_ug RGB_TOG``` and other keycodes to the keymap adjust layer.
While RGB is disabled, any RGB control keys
  behave as transparent keys and activate keys on lower layers,
  which can be confusing.

The Hillside shield definition files should *not* need to be modified and are in ./config/boards/shields.

More information about each keymap is in their readme files.

# Dongle mode (PandaKB USB dongle)

The Hillside 52 can also run through [PandaKB's ZMK dongle](https://pandakb.com/shop/keyboard-kit/pandakb-zmk-split-keyboard-dongle/)
(a nice!nano v2 with a 1.3" OLED). The dongle becomes the split **central** and both halves become its
**peripherals**: plug it into a computer and the keyboard just works as a USB keyboard, with no Bluetooth pairing
on that computer. ZMK fixes each part's role at build time, so in dongle mode the halves cannot connect to a
computer without the dongle, and switching modes means reflashing. Both sets of firmware are built.

| Artifact | Flash to | Mode |
|---|---|---|
| `hillside52_dongle` | dongle | dongle |
| `hillside52_left_dongle_mode` | left half | dongle |
| `hillside52_right-nice_nano_v2-zmk` | right half | **both** (the right half is a peripheral either way) |
| `hillside52_left-nice_nano_v2-zmk` | left half | standalone |
| `settings_reset-nice_nano_v2-zmk` | any of the three | reset (all are nice!nanos) |

**One-time setup:** turn off other ZMK keyboards nearby, flash the settings reset to all three devices
(standalone-mode bonds must be cleared first), then flash the dongle-mode firmware, plug in the dongle, and power
on both halves. To go back, reset the halves and flash the standalone left firmware.

- Keymap changes ([config/hillside52.keymap](config/hillside52.keymap)) then only need the **dongle** reflashed.
  To reach its bootloader without opening its case, hold both far outer thumbs (System layer) and hold `G` for 2
  seconds. (`T` on that layer is Bluetooth clear, so the dongle hold sits directly below it.) The top outer
  corners still bootloader each half.
- The dongle keeps all five Bluetooth profiles (its connection limits are raised to fit both halves too), so it
  can also pair to computers wirelessly on its own battery.
- Layers are named so the dongle's OLED can show them. The dongle never deep-sleeps: deep sleep is only woken by
  a key press, and it has no keys.
- The left half's shield used to set ZMK's legacy `ZMK_SPLIT_BLE_ROLE_CENTRAL`, which forces central mode through a
  `select` that no build argument can override. It now sets the modern `ZMK_SPLIT_ROLE_CENTRAL` as a plain
  default: same result standalone, but overridable for dongle mode.
