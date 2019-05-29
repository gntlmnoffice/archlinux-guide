# Arch installation
Steps to install arch in my pc. This document shows how to install arch using a wifi usb stick and how to set up dual boot with windows.

#### Verify the boot mode
- Run `ls /sys/firmware/efi/efivars`. If the directory does not exist, the system may be booted in *BIOS* or *CSM* mode.

#### Connect to the internet
- Run `wifi-menu` to set up the wifi. 
- Run `ping google.com` to test the conection.

#### Update the system clock

- Run `timedatectl set-ntp true` to ensure the system clock is accurate.
- Run `timedatectl status` to check the service status.

>Question: Why is this step is required?

#### Partition the disks
- Run `lsblk` to list the devices.
- Run `fdisk /dev/sdX` to partition a disk.
  This will take you to a new command prompt:
    - `m` for **help**.
    - `p` to **print** the state of the drive.
    - `d` to **delete** partition.
    - `n` to create a **new** partition.
    - `w` to write the changes.
- You need the following partitions:
  - *EFI* (this one is shared with Windows).
  - *Swap* (I am using 20 GB).
  - *Root* (I am using 30 GB).
  - *Home* (I am using the rest of the SSD disk).

#### Format partitions
- Run `mkfs.ext4` to format the *Home* and *Swap* partitions.
- Run `mkswap /dev/sdX2` and then `swapon /dev/sdX2` to initialize the *Swap* partition.

#### Mount the file systems
- Run `mkdir /mnt/home` to create the directory to mount the *Home* partition.
- Using the `mount` command, mount: 
  - The *Root* partition to `/mnt`.
  - The *Home* partition to `/mnt/home`.
  - The *EFI* partition to `/?????`.
  
>Question: Where to mount the EFI partition?

#### Install Arch Linux
- Run `pacstrap /mnt base base-devel`

#### Set up fstab
- Run `genfstab -U /mnt >> /mnt/etc/fstab` to generate the fstab file

#### Change root into the new system
- Run `arch-chroot /mnt`

#### Set up time zone
- Run `ln -sf /usr/share/zoneinfo/America/New_York /etc/localtime` to set the time zone.
- Run `hwclock --systohc` to generate `/etc/adjtime`

#### Set up localization
- Uncomment `en_US.UTF-8 UTF-8`, `en_US ISO-8859-1` in `/etc/locale.gen`.
- Run `locale-gen` to generate them.
- Create the file `/etc/locale.conf`, and add the line:
```
LANG=en_US.UTF-8
```

#### Set up root password
- Run `passwd` to set up the password

#### Set host name
- Create the file `/etc/hostname`, and add the line:
```
name-of-your-computer
```

#### Set up internet access
>Question: How to do this?

#### Set up GRUB
>Question: How to do this?

#### Reboot
- Run `exit` to go back to the usb drive.
- Run `umount -R /mnt` to unmount all the drive.
- Run `reboot` to restart the computer. Remember to remove the installation drive
