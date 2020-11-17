# Installation
My personal guide to install *archlinux*.

**Table of Contents**
- [1. Boot from the flash installation media](#1-boot-from-the-flash-installation-media)
- [2. Verify the boot mode](#2-verify-the-boot-mode)
- [3. Connect to the internet](#3-connect-to-the-internet)
- [4. Connect via ssh (optional)](#4-connect-via-ssh-optional)
- [5. Update the system clock](#5-update-the-system-clock)
- [6. Partition the disks](#6-partition-the-disks)
- [7. Format partitions](#7-format-partitions)
- [8. Initialize swap partition](#8-initialize-swap-partition)
- [9. Mount the partitions](#9-mount-the-partitions)
- [10. Install Arch Linux and all the main packages](#10-install-arch-linux-and-all-the-main-packages)
- [11. Set up fstab](#11-set-up-fstab)
- [12. Change root into the new system](#12-change-root-into-the-new-system)
- [13. Set up time zone](#13-set-up-time-zone)
- [14. Set up localization](#14-set-up-localization)
- [15. Set up root password](#15-set-up-root-password)
- [16. Set up network (wireless)](#16-set-up-network-wireless)
- [17. Set up GRUB](#17-set-up-grub)
- [18. Reboot](#18-reboot)

## 1. Boot from the flash installation media
Plug the flash installation media and boot the computer from it.

## 2. Verify the boot mode
- Run `ls /sys/firmware/efi/efivars`. If the directory does not exist, the system may be booted in *BIOS* or *CSM* mode.

## 3. Connect to the internet
- Ethernet—plug in the cable.
- Wi-Fi—authenticate to the wireless network using [iwctl](https://wiki.archlinux.org/index.php/Iwd#iwctl).
  - Run `iwctl`.
  - Run `device list` to list all wireless devices, make note of the *device name*, e.g. `wlan0`.
  - Run `station <device-name> get-networks`, make note of the *network name*, e.g. `Sandy & Dia or Sadia for short`.
  - Run `station <device-name> connect <network-name>` and enter the passphrase (AKA password).
  - Run `exit` to exit iwctl.
- Run `ping google.com` to test the conection.

## 4. Connect via ssh (optional)

It may be convenient to connect to the machine we want to install archlinux remotely from another machine
to easily copy the commands, etc. This step of course is completely optional.

On the remote machine (the one we want to install archlinux on):
  - Run `passwd` to set up the password.
  - Open the file `/etc/ssh/sshd_config` and check that `PermitRootLogin` yes is present (and uncommented).
  - Run `systemctl start sshd.service`.
  - Run `ifconfig -a` to get the ip address of the machine.

On the local machine:
  - ssh `root@<ip-address-of-the-machine>` and enter `yes` and then the password

[More [here](https://wiki.archlinux.org/index.php/Install_Arch_Linux_via_SSH)]

## 5. Update the system clock
- Run `timedatectl set-ntp true` to ensure the system clock is accurate.
- Run `timedatectl` and check that `NTP service: active`

## 6. Partition the disks
- Run `lsblk` to list the devices.
- Run `gdisk /dev/sdX` to partition the disk.
- *EFI* (seems to be required for UEFI):
  A separate /boot partition is only required if your boot loader is not capable of
  accessing the `/boot` directory that resides in `/`. This is not needed with *GRUB*.
  - *size*: `260–512 MB` (example: `+300m`)
  - *type:* `EFI System`, it may be shared with Windows.
- *swap* (optional):
  I prefer swap files. Swap files and swap partitions are equally performant, but swap files are much easier to resize as needed
  - *size*: `R + sqrt(R)`, where `R` is the size of the RAM
  - *type:* `Linux swap`. Run the command `free` to get the size of the RAM
- *root*:
  This is the required partition.
  - *size*: at least `32G`
  - *type:* `Linux x86-64 root (/)`.
- *home* (optional):
  It may be convenient to have your home on a different partition, but I don't really get any
  advantage with my setup and is a pain having to guess how much will I end needing for root and home.
  So I usually go with just one root partition.
  - *size*: Use the remaining space.
  - *type:* `Linux filesystem`.

## 7. Format partitions
- Run `mkfs.ext4` to format the *root* and *home* partitions.

  Example:
  ```
  mkfs.ext4 /dev/sdX#
  ```
- Run `mkfs.fat -F32 /dev/sdX#` to format the *EFI* partition

## 8. Initialize swap partition
>  **Note**: This is step is only needed if you created swap partition.

- To initialize the *swap* partition, run:
  ```
  mkswap /dev/sdX#
  swapon /dev/sdX#
  ```

## 9. Mount the partitions
- Using the `mount` command, mount the *root* partition to `/mnt`.`
  Example:
  ```
  mount /dev/sdX# /mnt
  ```
- Run: `mkdir /mnt/boot /mnt/home` to create the mounting points for the *EFI* and *home* partitions.
  Don't include `/mnt/home` if you didn't create a home partition.
- Using the `mount` command, mount the *EFI* partition to `/mnt/boot` and the *home* partition to `/mnt/home` (if created).

## 10. Install Arch Linux and all the main packages
- Run `pacstrap /mnt base linux linux-firmware base-devel`

## 11. Set up fstab
- Run `genfstab -U /mnt >> /mnt/etc/fstab` to generate the fstab file
- If there are any *NTFS* partitions, edit `/mnt/etc/fstab` and change the value of the **options** column to `defaults,umask=0007,gid=sandy`.

## 12. Change root into the new system
- Run `arch-chroot /mnt`

## 13. Set up time zone
- Run `ln -sf /usr/share/zoneinfo/America/New_York /etc/localtime` to set the time zone.
- Run `hwclock --systohc` to generate `/etc/adjtime`

## 14. Set up localization
- Uncomment `en_US.UTF-8 UTF-8`, `en_US ISO-8859-1` in `/etc/locale.gen`, to install neovim run `pacman -Syu neovim`.
- Run `locale-gen` to generate them.
- Create the file `/etc/locale.conf`, and add the line:
  ```
  LANG=en_US.UTF-8
  ```

## 15. Set up root password
- Run `passwd` to set up the password

## 16. Set up network (wireless)
- Run `pacman -Syu dialog wpa_supplicant dhcpcd netctl networkmanager` to install the required packages.

>**Note**: These packages should be enough to get `wifi-menu` with `netctl` and `NetworManager` working.

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

## 17. Set up GRUB
- Run `pacman -Syu grub efibootmgr os-prober` to install the required packages.
- Run `grub-install --target=x86_64-efi --efi-directory=/boot --bootloader-id=arch` to install grub.
- If dualbooting, make sure the windows partition is mounted.
- Run `grub-mkconfig -o /boot/grub/grub.cfg` to generate grub configuration file.

## 18. Reboot
- Run `exit` to go back to the usb drive.
- Run `umount -R /mnt` to unmount all the drive.
- Run `reboot` to restart the computer. Remember to remove the installation drive
