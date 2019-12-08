# Arch installation
Steps to install arch in my pc. This document shows how to install arch using a wifi usb stick and how to set up dual boot with windows. Find the official intallation guide [here](https://wiki.archlinux.org/index.php/Installation_guide).

### Flash installation media

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

### Windows

#### Partition Windows drive if needed
Follow the instructions described [here](https://www.howtogeek.com/101862/how-to-manage-partitions-on-windows-without-downloading-any-other-software/)

#### Disable fast start-up on Windows
Follow the steps described [here](https://www.tenforums.com/tutorials/4189-turn-off-fast-startup-windows-10-a.html).

#### Disable hibernation in Windows
Run `powercfg -h off` to disable hibernation in Windows in the cmd as administrator.

#### Disable secure boot
In the boot menu disable secure boot.

#### Bitlocker
I haven't figure out how to disable bitlocker. Make sure the computer doesn't have bit locker, if it does, copy the recovery key just in case.

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
- Run `pacman -Syu dialog wpa_supplicant dhcpcd netctl` to install the required packages.
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

### Post installation

#### Connect to wifi by default
- Run `wifi-menu` and create a profile. Name it something memorable like **home**
- Run `netctl enable <name-of-your-profile>` and next time you boot it should connect to the wifi automatically.

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

#### Set up desktop environment

##### Install gnome
- Run `sudo pacman -Syu gnome`
- Create the file `~/.xinitrc` and add the following line:
  ```
  export XDG_SESSION_TYPE=x11
  export GDK_BACKEND=x11
  exec gnome-session
  ```
  This will automatically start dwm when xorg starts

##### Install X
- Run `sudo pacman -Syu xorg-server xorg-xinit xorg-xrandr xorg-xsetroot` to install xorg
- Create the file `/etc/X11/Xwrapper.config` and add the following content to allow xinit to run from non-root users:
  ```
  allowed_users=anybody
  needs_root_rights=yes
  ```
- Run `xinit` to start the server

#### Install main packages
- Install **yay**:
  ```
  sudo pacman -Syu git
  git clone https://aur.archlinux.org/yay.git
  cd yay
  makepkg -si
  cd ..
  rm -r yay
  ```
- Install **audio**:
  - Add user to the audio group:
    ```
    sudo usermod -a -G audio <user-name>
    ```
  - Install audio utilities:
    ```
    sudo pacman -Syu pulseaudio pavucontrol
    ```
  - Restart the computer, audio should be working.

- Install **drivers** and configure nvidia:
  ```
  sudo pacman -Syu ntfs-3g nvidia
  nvidia-xconfig
  ```
- Install **fonts**:
  ```
  yay -S all-repository-fonts
  ```
- Install **codecs**:
  ```
  sudo pacman -Syu mpv
  ```

#### Install applications

  ```
  yay -S google-chrome
  sudo pacman -Syu firefox code ranger sxhkd git zip unzip vlc blender krita cura 
  ```
  - Install [Plex](https://wiki.archlinux.org/index.php/Plex#Installation).
  
>Note: see the official [List of application](https://wiki.archlinux.org/index.php/List_of_applications) from the wiki for more.

### Settings

#### Gnome utilities
- Install gnome tweaks:
```
sudo pacman -Syu gnome-tweaks
```

#### Install cursor theme
- Run:
  ```
  yay -S breeze-snow-cursor-theme
  ```
- The packages install the cursor in `/usr/share/icons/Breeze_Snow`, copy the folder and follow the instructions listed [here](https://wiki.archlinux.org/index.php/Cursor_themes#XDG_specification).

#### Enable numlock on start
- Run:
```
 sudo pacman -Syu numlockx
```
- Add it to the `~/.xinitrc` file before `exec`:
```
numlockx &
```
>Note: More [here](https://wiki.archlinux.org/index.php/Activating_numlock_on_bootup#startx)

#### Auto hide cursor
```
 sudo pacman -Syu unclutter
```
- Add it to the `~/.xinitrc` file before `exec`:
```
unclutter &
```
>Note: More [here](https://wiki.archlinux.org/index.php/Unclutter)

#### Enable hibernation
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
>Note: More [here](https://wiki.archlinux.org/index.php/Power_management/Suspend_and_hibernate#Hibernation).
