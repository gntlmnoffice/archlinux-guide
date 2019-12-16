
### Installation

#### Boot from the flash installation media
Plug the flash installation media and boot the computer from it.

#### Verify the boot mode
- Run `ls /sys/firmware/efi/efivars`. If the directory does not exist, the system may be booted in *BIOS* or *CSM* mode.

#### Connect to the internet
- Run `wifi-menu` to set up the wifi. 
- Run `ping google.com` to test the conection.

#### Update the system clock
- Run `timedatectl set-ntp true` to ensure the system clock is accurate.
- Run `timedatectl` to check the service status.

#### Partition the disks
- Run `lsblk` to list the devices.
- Run `gdisk /dev/sdX` to partition the disk.
- *EFI*: 
  - *size*: `260–512 MB` (example: `+260m`)
  - *type:* `EFI System`, it may be shared with Windows.
- *swap*: 
  - *size*: `R + sqrt(R)`, where `R` is the size of the RAM 
  - *type:* `Linux swap`. Run the command `free` to get the size of the RAM
- *root*: 
  - *size*: use the remaining space 
  - *type:* `Linux x86-64 root (/)`.

#### Format partitions
- Run `mkfs.ext4` to format the *root* partitions.

  Example:
  ```
  mkfs.ext4 /dev/sdX#
  ```
- Run `mkfs.fat -F32 /dev/sdX#` to format the *EFI* partition
  
#### Initialize swap partition
- To initialize the *swap* partition, run:
  ```
  mkswap /dev/sdX#
  swapon /dev/sdX#
  ```

#### Mount the file systems
- Using the `mount` command, mount the *root* partition to `/mnt`.`
  Example: 
  ```
  mount /dev/sdX# /mnt
  ```
- Run: `mkdir /mnt/boot` to create the mounting point for the *EFI* partition
- Using the `mount` command, mount the *EFI* partition to `/mnt/boot`.
  
#### Install Arch Linux and all the main packages
- Run `pacstrap /mnt base linux linux-firmware base-devel`

#### Set up fstab
- Run `genfstab -U /mnt >> /mnt/etc/fstab` to generate the fstab file
- For the *NTFS* partitions, edit `/mnt/etc/fstab` and change the value of the **options** column to `defaults,umask=0007,gid=sandy`.

#### Change root into the new system
- Run `arch-chroot /mnt`

#### Set up time zone
- Run `ln -sf /usr/share/zoneinfo/America/New_York /etc/localtime` to set the time zone.
- Run `hwclock --systohc` to generate `/etc/adjtime`

#### Set up localization
- Uncomment `en_US.UTF-8 UTF-8`, `en_US ISO-8859-1` in `/etc/locale.gen`, to install neovim run`pacman -Syu neovim`.
- Run `locale-gen` to generate them.
- Create the file `/etc/locale.conf`, and add the line:
  ```
  LANG=en_US.UTF-8
  ```

#### Set up root password
- Run `passwd` to set up the password

#### Set up network (wireless)
- Run `pacman -Syu dialog wpa_supplicant dhcpcd netctl networkmanager` to install the required packages.

>Note: These packages should be enough to get `wifi-menu` with `netctl` and `NetworManager` working.

- Create the file `/etc/hostname`, and add the line:
  ```
  your-computer-name
  ```
- Edit the file `/etc/hosts`, and add the lines:
  ```
  127.0.0.1	localhost
  ::1		localhost
  127.0.1.1	your-computer-name.localdomain	your-computer-name
  ```

#### Set up GRUB
- Run `pacman -Syu grub efibootmgr os-prober` to install the required packages.
- Run `grub-install --target=x86_64-efi --efi-directory=/boot --bootloader-id=arch` to install grub.
- If dualbooting, make sure the windows partition is mounted.
- Run `grub-mkconfig -o /boot/grub/grub.cfg` to generate grub configuration file.

#### Reboot
- Run `exit` to go back to the usb drive.
- Run `umount -R /mnt` to unmount all the drive.
- Run `reboot` to restart the computer. Remember to remove the installation drive
