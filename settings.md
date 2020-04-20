# Settings
Different settings to configure *archlinux* to my liking.

## Install the LTS kernel
- Run `uname -r` to check the version of of the kernel, if there is no `lts` in the name it's not and LTS version.
- Install `linux-lts` and `linux-lts linux-lts-headers`.
- Run `sudo grub-mkconfig -o /boot/grub/grub.cfg` to configure the GRUB bootloader.
- Reboot, and select the LTS kernel in Advanced options of the GRUB menu. After the boot, check if you indeed use the LTS kernel with `uname -r`.
- If everything is fine, you can remove the non-lts kernel. Run `sudo pacman -Rs linux`

## Enable numlock on start
- Run:
```
 sudo pacman -Syu numlockx
```
- Add it to `~/.xinitrc` file before `exec`:
  ```
  numlockx &
  ```
>**Note**: More [here](https://wiki.archlinux.org/index.php/Activating_numlock_on_bootup#startx)

## Automount drives
- Run:
  ```
  sudo pacman -Syu udiskie
  ```
- Add `udiskie &` to `~/.xinitrc` before `exec startx`.

## Auto hide cursor
- Run:
  ```
   sudo pacman -Syu unclutter
  ```
- Add `unclutter &` to `~/.xinitrc` before `exec startx`.

## Synchronize clock
- Run `systemctl enable --now systemd-timesyncd.service` to start the service now and on boot.
- Run `timedatectl` to check the status, it takes a few seconds too load.

>**Note**: More [here](https://wiki.archlinux.org/index.php/Systemd-timesyncd)

## Enable auto login in tty
- Create the file `/etc/systemd/system/getty@tty1.service.d/override.conf` and add the following content:
```
[Service]
ExecStart=
ExecStart=-/usr/bin/agetty --autologin username --noclear %I $TERM
```

## Auto start x
- Add the following line add the end of `~/.bash_profile`:
```
exec startx
```

## Enable hibernation
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
- Run: `sudo systemctl hibernate` to hibernate.
>**Note**: More [here](https://wiki.archlinux.org/index.php/Power_management/Suspend_and_hibernate#Hibernation).

## Set up rclone to sync files with cloud
- Install `rclone` from the archlinux repo.
- Run `rclone config` to configure it. [Here](https://rclone.org/docs/) you can find documentation for the different services available. Documentation specific
 to Google Drive can be found [here](https://rclone.org/drive/).
  > **Note**: In the case of Google Drive, for the `--drive-root-folder-id` you must specify the `id` of the folder, not the 
  path. The folder `id` can be found at the end of the url of the folder.
- Add `rclone mount remote:path/to/files /path/to/local/mount &` to `~/.xinitrc`. More info on the `rclone mount` command can be found [here](https://rclone.org/commands/rclone_mount/).
> **Note:**: More info on `rclone` can be found on the [rclone docs](https://rclone.org/docs/)

## Install list of words for spell checker
- Install `words` from the official repo.
  This adds some files to the folder `/usr/share/dict/`, each file contains a list of words in a given language. 
  For words in english, use `/usr/share/dict/words`.

## Customize GRUB
- Install `grub-customizer` and run it with `sudo` to customize grub

## Set up slock as a locker

- Install `xss-lock` and `slock`.
- Add the following lines to `~/.xinitrc`:
```
# screen locker
xset s on & # enable the screensaver
xset s 600 & # launch screensaver after 10 mins of inactivity
xss-lock slock & # start slock as a locker when the screensaver is triggered 
slock & # run locker on start
```
- Run `xset s activate` to manually trigger the screensaver that triggers the locker

## Set wallpaper as a solid color
- Install `hsetroot` from the official repo.
- Run `hsetroot -solid <color>`, for example in use `hsetroot -solid '#232729'`. 
- Add it to your `~/.xinitrc`.

## Disable action when lid closes
- Edit `/etc/systemd/logind.conf` and make set:
```
HandleLidSwitch=ignore
```
- Run the following command (it will restart your computer):
```
systemctl restart systemd-logind
```
>**Note**: More [here](https://wiki.archlinux.org/index.php/Power_management#Power_management_with_systemd)

## Fix screen tearing
- Use [this video](https://www.youtube.com/watch?v=MfL_JkcEFbE) to determine if the screen is tearing.
- To avoid tearing install `picon` and run it on start. This is a compositor, by default it adds shadows and fading animations, these effects can be disabled in the config.
>**Issue**: Adding `picon &` to `~/.xinitrc` causes a gray screen to appear for a second before the window manager starts. This behavior disappears if the locker autostarts.

## Configure GTK and Qt themes
- Install a *GTK* theme. I use *Adwaita*, to install it, install `gtk3` for the *GTK 3* version and `gnome-themes-extra` for the *GTK 2* version.
- Install a cursor theme. I use *Breeze Snow*, to install it, install `breeze-snow-cursor-theme` from the *AUR*.
- Install an icon theme. I use *Papirus Dark*, to install it, install `papirus-gtk-icon-theme` from the *AUR*.
- Install `lxappearance` to set the theme for GTK 2 and 3.
- Use *QGtkStyle* to extend the *GTK* theme to *Qt*
  - Install `qt5-styleplugins`.
  - For *Qt 5* set the envirment varialble `QT_QPA_PLATFORMTHEME=gtk2`.
  - For *Qt 4* see instructions [here](https://wiki.archlinux.org/index.php/Uniform_look_for_Qt_and_GTK_applications#QGtkStyle) (I haven't done this step because I haven't needed it)
  > **Note**: Make sure your GTK theme is compatible with GTK 2 for this method to work

  > **Note**: More [here](https://wiki.archlinux.org/index.php/Uniform_look_for_Qt_and_GTK_applications#QGtkStyle)

### Change gtk theme on the fly
- Install `xfconf` and `xfsettingsd` from the official repo.
- Add `xfsettingsd &` to `~/.xinitrc`
- Run the following to set the theme to Adwaita-dark with Papirus-Dark icons:
  ```
  xfconf-query --create -c xsettings -p /Net/ThemeName -t string -s Adwaita-dark
  xfconf-query --create -c xsettings -p /Net/IconThemeName -t string -s Papirus-Dark
  ```

