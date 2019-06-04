# Arch installation
Steps to install arch in my pc. This document shows how to install arch using a wifi usb stick and how to set up dual boot with windows.

#### Make shure Windows is not hibernating

Run `powercfg -h off` to disable hibernation in Windows in the cmd as administrator.

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
- Run `mkfs.ext4` to format the *Home* and *Root* partitions.

  Example:
  ```
  mkfs.ext4 /dev/sdX1
  ```
  
#### Initialize swap partition
- Run `mkswap /dev/sdX2` and then `swapon /dev/sdX2` to initialize the *Swap* partition.

#### Mount the file systems
- Using the `mount` command, mount the *Root* partition to `/mnt`.`
  Example: 
  ```
  mount /dev/sdX1 /mnt
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
- Go to the generated file `/mnt/etc/fstab` and change the value of the **options** column to `defaults,umask=0000` for the *Windows*, *Empty*, *Software* and *Main* entries.

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
- Run `pacman -S dialog wpa_supplicant` to install the required packages to set up the wifi.


#### Set up GRUB
- Run `pacman -S grub efibootmgr os-prober` to install the required packages.
- Run `grub-install --target=x86_64-efi --efi-directory=/boot --bootloader-id=GRUB` to install grub.
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
- Install **drivers** and configure nvidia:
  ```
  pacman -S ntfs-3g nvidia
  nvidia-xconfig
  ```
- Install **fonts**:
  ```
  pacman -S noto-fonts ttf-liberation ttf-dejavu ttf-roboto ttf-ubuntu-font-family
  ```
-Install **applications**:
  ```
  pacman -S feh mlocate firefox code atom vim neovim ranger sxhkd git fish zip unzip
  ```

#### Set up dwm, st and dmenu
- Run `pacman -S libxinerama fontconfig libxft` to install the required dependencies.
- Go to the directories containing your version of the source for dwm, st and dmenu and run `make install` on each.
- Create the file `~/.xinitrc` and add the following line:
  ```
    exec dwm
  ```
  This will automatically start dwm when xorg starts

#### Set up xorg
- Run `pacman -S xorg-server xorg-xinit xorg-xrandr xorg-xsetroot` to install xorg
- Run `xinit` to start the server
- Create the file `/etc/X11/Xwrapper.config` and add the following content to allow xinit to run from non-root users:
  ```
  allowed_users=anybody
  needs_root_rights=yes
  ```
