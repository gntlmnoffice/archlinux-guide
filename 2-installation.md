# Installation
My personal guide to install *archlinux*.

**Table of Contents**
- [1. Boot from the flash installation media](#1-boot-from-the-flash-installation-media)
- [2. Verify the boot mode](#2-verify-the-boot-mode)
- [3. Connect to the internet](#3-connect-to-the-internet)
- [4. Update the system clock](#4-update-the-system-clock)
- [5. Partition the disks](#5-partition-the-disks)
- [6. Format partitions](#6-format-partitions)
- [7. Initialize swap partition](#7-initialize-swap-partition)
- [8. Mount the partitions](#8-mount-the-partitions)
- [9. Install Arch Linux and all the main packages](#9-install-arch-linux-and-all-the-main-packages)
- [10. Set up fstab](#10-set-up-fstab)
- [11. Change root into the new system](#11-change-root-into-the-new-system)
- [12. Set up time zone](#12-set-up-time-zone)
- [13. Set up localization](#13-set-up-localization)
- [14. Set up root password](#14-set-up-root-password)
- [15. Set up network (wireless)](#15-set-up-network-wireless)
- [16. Set up GRUB](#16-set-up-grub)
- [17. Reboot](#17-reboot)

## 1. Boot from the flash installation media
Plug the flash installation media and boot the computer from it.

## 2. Verify the boot mode
- Run `ls /sys/firmware/efi/efivars`. If the directory does not exist, the system may be booted in *BIOS* or *CSM* mode.

## 3. Connect to the internet
- Ethernet—plug in the cable.
- Wi-Fi—authenticate to the wireless network using [iwctl](https://wiki.archlinux.org/index.php/Iwd#iwctl).
  - Run `iwctl`
  - Run `device list` to list all wireless devices, make note of the *device name*, e.g. `wlan0`
  - Run `station <device-name> get-networks`, make note of the *network name*, e.g. `Sandy & Dia or Sadia for short`
  - Run `station <device-name> connect <network-name>` and enter the passphrase (AKA password)
  - Run `exit` to exit iwctl
- Run `ping google.com` to test the conection.

## 4. Update the system clock
- Run `timedatectl set-ntp true` to ensure the system clock is accurate.
- Run `timedatectl` to check the service status.

## 5. Partition the disks
- Run `lsblk` to list the devices.
- Run `gdisk /dev/sdX` to partition the disk.
- *EFI*:
  - *size*: `260–512 MB` (example: `+260m`)
  - *type:* `EFI System`, it may be shared with Windows.
- *swap*:
  - *size*: `R + sqrt(R)`, where `R` is the size of the RAM
  - *type:* `Linux swap`. Run the command `free` to get the size of the RAM
- *root*:
  - *size*: `23G - 32G`
  - *type:* `Linux x86-64 root (/)`.
- *home*:
  - *size*: Use the remaining space.
  - *type:* `Linux filesystem`.

## 6. Format partitions
- Run `mkfs.ext4` to format the *root* and *home* partitions.

  Example:
  ```
  mkfs.ext4 /dev/sdX#
  ```
- Run `mkfs.fat -F32 /dev/sdX#` to format the *EFI* partition

## 7. Initialize swap partition
- To initialize the *swap* partition, run:
  ```
  mkswap /dev/sdX#
  swapon /dev/sdX#
  ```

## 8. Mount the partitions
- Using the `mount` command, mount the *root* partition to `/mnt`.`
  Example:
  ```
  mount /dev/sdX# /mnt
  ```
- Run: `mkdir /mnt/boot /mnt/home` to create the mounting points for the *EFI* and *home* partitions.
- Using the `mount` command, mount the *EFI* and *home* partitions to `/mnt/home` and `/mnt/boot` respectively.

## 9. Install Arch Linux and all the main packages
- Run `pacstrap /mnt base linux linux-firmware base-devel`

## 10. Set up fstab
- Run `genfstab -U /mnt >> /mnt/etc/fstab` to generate the fstab file
- If there are any *NTFS* partitions, edit `/mnt/etc/fstab` and change the value of the **options** column to `defaults,umask=0007,gid=sandy`.

## 11. Change root into the new system
- Run `arch-chroot /mnt`

## 12. Set up time zone
- Run `ln -sf /usr/share/zoneinfo/America/New_York /etc/localtime` to set the time zone.
- Run `hwclock --systohc` to generate `/etc/adjtime`

## 13. Set up localization
- Uncomment `en_US.UTF-8 UTF-8`, `en_US ISO-8859-1` in `/etc/locale.gen`, to install neovim run `pacman -Syu neovim`.
- Run `locale-gen` to generate them.
- Create the file `/etc/locale.conf`, and add the line:
  ```
  LANG=en_US.UTF-8
  ```

## 14. Set up root password
- Run `passwd` to set up the password

## 15. Set up network (wireless)
- Run `pacman -Syu dialog wpa_supplicant dhcpcd netctl networkmanager` to install the required packages.

>Note: These packages should be enough to get `wifi-menu` with `netctl` and `NetworManager` working.

- Create the file `/etc/hostname`, and add the line:
  ```
  your-computer-name
  ```
- Edit the file `/etc/hosts`, and add the lines:
  ```
  127.0.0.1	localhost
  ::1 localhost
  127.0.1.1	your-computer-name.localdomain	your-computer-name
  ```

## 16. Set up GRUB
- Run `pacman -Syu grub efibootmgr os-prober` to install the required packages.
- Run `grub-install --target=x86_64-efi --efi-directory=/boot --bootloader-id=arch` to install grub.
- If dualbooting, make sure the windows partition is mounted.
- Run `grub-mkconfig -o /boot/grub/grub.cfg` to generate grub configuration file.

## 17. Reboot
- Run `exit` to go back to the usb drive.
- Run `umount -R /mnt` to unmount all the drive.
- Run `reboot` to restart the computer. Remember to remove the installation drive
