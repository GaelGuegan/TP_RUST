# Raspberry Pico

## Prerequisites
In order to compile and flash the firmware to the raspberry pico, some tools are needed.

- Download the pico toolchain:
`rustup target add thumbv6m-non-eabi`
- If needed install the dependencies:
`sudo apt install pkg-config libudev-dev`
- Install the elf to uf2 converter:
`cargo install elf2uf2-rs --locked`

## Compilation
- `cargo build` will create an ELF file named `blinky` in `target/thumbv6m-none-eabi/debug` by default
- `cargo build --release` will create an ELF file in `target/thumbv6m-none-eabi/release`, which is optimized and without debug symbol

However the raspberry pico, only accept file in UF2 format.

So the elf file can be converted with: `elf2uf2-rs target/thumbv6m-none-eabi/release/blinky` will create a new file `blinky.uf2`

That is why the instruction in the config file `runner = "elf2uf2-rs -d"` is here. It is used to convert the elf file and flash it to the target.

## Boot RP2040 in bootloader mode
In order to flash the firmware, the pico board need to be **put in bootloader mode**.

This is done by rebooting whilst holding some kind of "Boot Select" button.

## Flash

### Option 1: from Windows
Directly **drag and drop** the file to the usb storage volume that appears in your filesystem.

### Option 2: from WSL
By default WSL does not have access to USB storage.

This is done by using USB/IP opensource project, which needs to have WSL version 2 to be installed in a first time. More details [here](https://learn.microsoft.com/en-us/windows/wsl/connect-usb)

- Install WSL 2: (this could be very long)
```shell
wsl --set-version Ubuntu 2
```
- If an error happened, it could be needed to enable the VM feature and install the kernel package:
```shell
> Enable-WindowsOptionalFeature -Online -FeatureName VirtualMachinePlatform
> DL kernel package: https://wslstorestorage.blob.core.windows.net/wslblob/wsl_update_x64.msi
> wsl update
```
- Install USB/IP project:
```shell
winget install --interactive --exact dorssel.usbipd-win
```
- Attach your USB device to WSL:
```shell
usbipd list
usbipd bind --busid <busid>
usbipd attach --wsl --busid <busid>
```
- Check your USB devices in WSL, and mount it:
```shell
lsusb
sudo mkdir /mnt/d
sudo mount -t drvfs D: /mnt/d
```
- Build firmware and flash it with:
```shell
cargo run --bin blinky
```

## Links

https://www.alexdwilson.dev/learning-in-public/how-to-program-a-raspberry-pi-pico
https://github.com/aptlyundecided/super-blank-rust-pico-project
https://crates.io/crates/rp-pico
https://github.com/rp-rs/rp-hal/blob/main/rp2040-hal/examples/pio_blink.rs
https://www.raspberrypi.com/documentation/microcontrollers/images/pico-pinout.svg