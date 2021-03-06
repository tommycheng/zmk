---
id: dev-build-flash
title: Building and Flashing
sidebar_label: Building and Flashing
---

import Tabs from '@theme/Tabs';
import TabItem from '@theme/TabItem';

export const OsTabs = (props) => (<Tabs
groupId="operating-systems"
defaultValue="debian"
values={[
{label: 'Debian/Ubuntu', value: 'debian'},
{label: 'Raspberry OS', value: 'raspberryos'},
{label: 'Fedora', value: 'fedora'},
{label: 'Windows', value: 'win'},
{label: 'macOS', value: 'mac'},
]
}>{props.children}</Tabs>);

## Building

From here on, building and flashing ZMK should all be done from the `app/` subdirectory of the ZMK checkout:

```sh
cd app
```

To build for your particular keyboard, the behaviour varies slightly depending on if you are building for a keyboard with
an onboard MCU, or one that uses an MCU board addon.

### Keyboard (Shield) + MCU Board

ZMK treats keyboards that take an MCU addon board as [shields](https://docs.zephyrproject.org/2.3.0/guides/porting/shields.html), and treats the smaller MCU board as the true [board](https://docs.zephyrproject.org/2.3.0/guides/porting/board_porting.html)

Given the following:

- MCU Board: Proton-C
- Keyboard PCB: kyria_left
- Keymap: default

You can build ZMK with the following:

```sh
west build -b proton_c -- -DSHIELD=kyria_left
```

### Keyboard With Onboard MCU

Keyboards with onboard MCU chips are simply treated as the [board](https://docs.zephyrproject.org/2.3.0/guides/porting/board_porting.html) as far as Zephyr™ is concerned.

Given the following:

- Keyboard: Planck (rev6)
- Keymap: default

you can build ZMK with the following:

```sh
west build -b planck_rev6
```

### Pristine Building
When building for a new board and/or shield after having built one previously, you may need to enable the pristine build option. This option removes all existing files in the build directory before regenerating them, and can be enabled by adding either --pristine or -p to the command:

```sh
west build -p -b proton_c -- -DSHIELD=kyria_left
```
### Building For Split Keyboards

:::note
For split keyboards, you will have to build and flash each side separately the first time you install ZMK.
:::

By default, the `build` command outputs a single .uf2 file named `zmk.uf2` so building left and then right immediately after will overwrite your left firmware. In addition, you will need to pristine build each side to ensure the correct files are used. To avoid having to pristine build every time and separate the left and right build files, we recommend setting up separate build directories for each half. You can do this by using the `-d` parameter and first building left into `build/left`:

```
west build -d build/left -b nice_nano -- -DSHIELD=kyria_left
```
and then building right into `build/right`:
```
west build -d build/right -b nice_nano -- -DSHIELD=kyria_right
```
This produces `left` and `right` subfolders under the `build` directory and two separate .uf2 files. For future work on a specific half, use the `-d` parameter again to ensure you are building into the correct location.

## Flashing

Once built, the previously supplied parameters will be remembered so you can run the following to flash your
board with it in bootloader mode:

```
west flash
```

For boards that have drag and drop .uf2 flashing capability, the .uf2 file to flash can be found in `build/zephyr` (or `build/left|right/zephyr` if you followed the instructions for splits) and is by default named `zmk.uf2`.
