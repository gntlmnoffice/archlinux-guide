# Pre installation
Steps to follow before starting the install.

**Table of Contents**
- [1. Download the iso](#1-download-the-iso)
- [2. Prepare flash installation media](#2-prepare-flash-installation-media)
- [3. Partition Windows drive if needed](#3-partition-windows-drive-if-needed)
- [4. Disable fast start-up on Windows](#4-disable-fast-start-up-on-windows)
- [5. Disable hibernation in Windows](#5-disable-hibernation-in-windows)
- [6. Bitlocker](#6-bitlocker)
- [7. Disable secure boot](#7-disable-secure-boot)

## 1. Download the iso
Download the arch linux iso image [here](https://www.archlinux.org/download/). You could use transmission to download from a torrent or use a direct download. The iso size is 635.0 MB.

## 2. Prepare flash installation media
>**Note**: There are alternative methods to prepare the flash installation media, you could read about them [here](https://wiki.archlinux.org/index.php/USB_flash_installation_media#Using_automatic_tools).

- Find out the name of your USB drive with `lsblk`.
- Run the following command, replacing `/dev/sdX` with your drive, e.g. `/dev/sde`. (Do not append a partition number, so do **not** use something like `/dev/sdb1`)
```
sudo dd bs=4M if=path/to/archlinux.iso of=/dev/sdX status=progress oflag=sync
```
>**Tip**: If the USB's Arch ISO hangs or is unable to load, try repeating the dd media creation process on the same USB drive one or more times.

## 3. Partition Windows drive if needed
Follow the instructions described [here](https://www.howtogeek.com/101862/how-to-manage-partitions-on-windows-without-downloading-any-other-software/)

## 4. Disable fast start-up on Windows
Follow the steps described [here](https://www.tenforums.com/tutorials/4189-turn-off-fast-startup-windows-10-a.html).

## 5. Disable hibernation in Windows
Run `powercfg -h off` to disable hibernation in Windows in the cmd as administrator.

## 6. Bitlocker
I haven't figure out how to disable bitlocker. Make sure the computer doesn't have bit locker, if it does, copy the recovery key just in case.

## 7. Disable secure boot
In the boot menu disable secure boot.
