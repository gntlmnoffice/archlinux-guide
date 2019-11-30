# Arch installation
Steps to install arch in my pc. This document shows how to install arch using a wifi usb stick and how to set up dual boot with windows. Find the official intallation guide [here](https://wiki.archlinux.org/index.php/Installation_guide).

#### Download the iso

Download the arch linux iso image [here](https://www.archlinux.org/download/). You could use transmission to download from a torrent or use a direct download. The iso size is 635.0 MB.

#### Prepare flash installation media

>Note: There are alternative methods to prepare the flash installation media, you could read about them [here](https://wiki.archlinux.org/index.php/USB_flash_installation_media#Using_automatic_tools).

- Find out the name of your USB drive with `lsblk`.
- Run the following command, replacing `/dev/sdX` with your drive, e.g. `/dev/sde`. (Do not append a partition number, so do **not** use something like `/dev/sdb1`)
```
sudo dd bs=4M if=path/to/archlinux.iso of=/dev/sdX status=progress oflag=sync
```
>Tip: If the USB's Arch ISO hangs or is unable to load, try repeating the dd media creation process on the same USB drive one or more times.

#### Disable fast start-up on Windows
Follow the steps described [here](https://www.tenforums.com/tutorials/4189-turn-off-fast-startup-windows-10-a.html).

#### Disable hibernation in Windows
Run `powercfg -h off` to disable hibernation in Windows in the cmd as administrator.

#### Disable secure boot
In the boot menu disable secure boot.

#### Boot from the flash installation media
Plug the flash installation media and boot the computer from it.

#### Verify the boot mode
- Run `ls /sys/firmware/efi/efivars`. If the directory does not exist, the system may be booted in *BIOS* or *CSM* mode.

#### Connect to the internet
- Run `wifi-menu` to set up the wifi. 
- Run `ping google.com` to test the conection.

#### Update the system clock

- Run `timedatectl set-ntp true` to ensure the system clock is accurate.
- Run `timedatectl status` to check the service status.

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
- Run `mkfs.ext4` to format the *Home* and *Root* partitions.

  Example:
  ```
  mkfs.ext4 /dev/sdX#
  ```
  
#### Initialize swap partition
- Run `mkswap /dev/sdX#` and then `swapon /dev/sdX#` to initialize the *Swap* partition.

#### Mount the file systems
- Using the `mount` command, mount the *Root* partition to `/mnt`.`
  Example: 
  ```
  mount /dev/sdX# /mnt
  ```
- Run `mkdir -p /mnt/{home,boot,mnt/windows,main,software,empty}}` to create the directories to mount the partitions.
- Using the `mount` command, mount:
  - The *Home* partition to `/mnt/home`.
  - The *EFI* partition to `/mnt/boot`.
  - The *Windows* partition to `/mnt/mnt/windows`.
  - The *Empty* partition to `/mnt/mnt/empty`.
  - The *Software* partition to `/mnt/mnt/software`. 
  - The *Main* partition to `/mnt/mnt/main`.
  
#### Install Arch Linux and all the main packages I use
- Run `pacstrap /mnt base base-devel`

#### Set up fstab
- Run `genfstab -U /mnt >> /mnt/etc/fstab` to generate the fstab file
- Go to the generated file `/mnt/etc/fstab` and change the value of the **options** column to `defaults,umask=0007,gid=sandy` for the *Windows*, *Empty*, *Software* and *Main* entries.

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

#### Set up network (wireless)
- Create the file `/etc/hostname`, and add the line:
```
name-of-your-computer
```
- Create the file `/etc/hosts`, and add the lines:
```
127.0.0.1	localhost
```
- Run `pacman -Syu dialog wpa_supplicant` to install the required packages to set up the wifi.

#### Set up GRUB
- Run `pacman -Syu grub efibootmgr os-prober` to install the required packages.
- Run `grub-install --target=x86_64-efi --efi-directory=/boot --bootloader-id=arch` to install grub.
- Make sure the windows partition is mounted
- Run `grub-mkconfig -o /boot/grub/grub.cfg` to generate grub configuration file.

#### Reboot
- Run `exit` to go back to the usb drive.
- Run `umount -R /mnt` to unmount all the drive.
- Run `reboot` to restart the computer. Remember to remove the installation drive

#### Connect to wifi by default
- Run `wifi-menu` and create a profile. Name it something memorable like **default**
- Run `netctl enable profile_name` and next time you boot it should connect to the wifi automatically.

#### Add user
- Run `useradd -m -g group_name user_name` to add a user.
  Example:
  ```
  useradd -m -g wheel sandy
  ```
- Run `passwd user_name` to set the password for a user.
- Edit `/etc/sudoers` and uncomment the line:
 ```
 %wheel ALL=(ALL) NOPASSWD: ALL
 ```
 This gives users of the *wheel* group sudo access without password.
- Run `logout` to log out from root.
- Enter your credentials to log as the newly created user
#### Install packages
- Install **yay**:
  ```
  git clone https://aur.archlinux.org/yay.git
  cd yay
  makepkg -si
  ```
- Install **drivers** and configure nvidia:
  ```
  pacman -Syu ntfs-3g nvidia
  nvidia-xconfig
  ```
- Install **fonts**:
  ```
  yay -S all-repository-fonts
  ```
- Install **codecs**:
  ```
  pacman -Syu mpv
  ```
- Install **audio** utilities:
  ```
  pacman -Syu pulseaudio pavucontrol pamixer
  ```
- Install **cursor**:
  ```
  yay -S breeze-snow-cursor-theme
  ```
  **Note**: The packages install the cursor in `/usr/share/icons/Breeze_Snow`, I copied the folder and followed the instructions from [here](https://wiki.archlinux.org/index.php/Cursor_themes#XDG_specification).

- Install **applications**:
  ```
  pacman -Syu feh mlocate firefox code atom neovim xsel ranger w3m xterm sxhkd git openssh fish zip unzip
  yay -S google-chrome numlockx unclutter
  ```
  - Install [Plex](https://wiki.archlinux.org/index.php/Plex#Installation).

#### Set up dwm, st and dmenu
- Run `pacman -Syu libxinerama fontconfig libxft` to install the required dependencies.
- Go to the directories containing your version of the source for dwm, st and dmenu and run `make install` on each.
- Create the file `~/.xinitrc` and add the following line:
  ```
  exec dwm
  ```
  This will automatically start dwm when xorg starts
  
#### Set up X
- Run `pacman -Syu xorg-server xorg-xinit xorg-xrandr xorg-xsetroot` to install xorg
- Run `xinit` to start the server
- Create the file `/etc/X11/Xwrapper.config` and add the following content to allow xinit to run from non-root users:
  ```
  allowed_users=anybody
  needs_root_rights=yes
  ```
  
#### Enable hibernation
>Note: For more information see [here](https://wiki.archlinux.org/index.php/Power_management/Suspend_and_hibernate#Hibernation).

- Run `lsblk` to find out the name of the SWAP partition.
- Open the file `/etc/default/grub`, find the line that starts with `GRUB_CMDLINE_LINUX_DEFAULT="` and add `resume=/dev/sdX#"`to the sequence of space separated paramenter within the quotation marks.
- Run the following command to generate the `grub.cfg` configuration file:  
  ```
  sudo grub-mkconfig -o /boot/grub/grub.cfg
  ```
- Open `/etc/mkinitcpio.conf`, find the list of HOOKS and add `resume`, it should appear after udev. For xample:
```
HOOKS=(base udev autodetect modconf block filesystems keyboard resume fsck)
```
- Run the following command to regenerate the initramfs for these changes to take effect.
```
sudo mkinitcpio -p linux
```

