### Settings

#### Gnome settings

##### Install gnome tweaks

- Run:
  ```
  sudo pacman -Syu gnome-tweaks
  ```

##### Set 12 hours time format
- Go to *System Settings* and select *Date & Time*. Use the drop down menu on *Time Format* to select *AM/PM*:


##### Disable gnome keyring
- Just enter a blank password when prompted

#### Start windows maximized
>**Note**: This is only needed if a tiled window manager is not used.

- Run:
  ```
  yay -S devilspie
  ```
- Create the file `~/.devilspie/maximize.ds` and add the following content:
  ```
  (begin
      (maximize)(focus)
  ) 
  ```
- Add `devilspie &` to `~/.xinitrc` file before `exec`.

#### Enable numlock on start
- Run:
```
 sudo pacman -Syu numlockx
```
- Add it to `~/.xinitrc` file before `exec`:
  ```
  numlockx &
  ```
>**Note**: More [here](https://wiki.archlinux.org/index.php/Activating_numlock_on_bootup#startx)


#### Automount USBs
- Run:
  ```
  sudo pacman -Syu udiskie
  ```
- Add `udiskie &` to `~/.xinitrc` before `exec startx`.

#### Auto hide cursor
- Run:
  ```
   sudo pacman -Syu unclutter
  ```
- Add `unclutter &` to `~/.xinitrc` before `exec startx`.

>**Note**: More [here](https://wiki.archlinux.org/index.php/Unclutter)

#### Enable auto login in tty
- Create the file `/etc/systemd/system/getty@tty1.service.d/override.conf` and add the following content:
```
[Service]
ExecStart=
ExecStart=-/usr/bin/agetty --autologin username --noclear %I $TERM
```

#### Auto start x
- Add the following line add the end of `~/.bash_profile`:
```
exec startx
```

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
- Run: `sudo systemctl hibernate` to hibernate.
>**Note**: More [here](https://wiki.archlinux.org/index.php/Power_management/Suspend_and_hibernate#Hibernation).

#### Customize GRUB
- Install `grub-customizer` and run it with `sudo` to customize grub

#### XSecureLock
This section explains how to set up *xsercurelock* with *xset* with *xss-lock* to automatically trigger the it.

- Install `xss-lock` and `xsercurelock`.
- Create the file `~/bin/locker` to configure and start `xsercurelock`
- Add the following lines to `~/.xinitrc`:
```
# Screen locker
xset s on &
xset s 600 &
xss-lock locker &
locker &
```
- Use `xset s activate` to manually trigger the locker (with key bindings for instance)
>**Issue**: I am calling `locker` directly in .xinitrc because `xset s activate` won't work. 

##### Custom screensaver for XSecureLock
- Create the file `/usr/lib/xsecurelock/saver_custom` with the following content:
```
#!/bin/bash
screensaver
```
- Create the file `~/bin/screensaver` to define the logic for your screensaver, you could base it off one of the files in `/usr/lib/xsecurelock/`
- Set the environment variable `export XSECURELOCK_SAVER="saver_custom"` in `~/bin/locker`

#### Disable action when lid closes
- Edit `/etc/systemd/logind.conf` and make set:
```
HandleLidSwitch=ignore
```
- Run the following command (it will restart your computer):
```
systemctl restart systemd-logind
```
>**Note**: More [here](https://wiki.archlinux.org/index.php/Power_management#Power_management_with_systemd)

#### Multiple monitors
- Install `arandr` a graphical interface for `xrandr` to manage multiple monitors, or use `xorg-xrandr` directly from the terminal instead.

#### Add Open in terminal option to PCManFM
- Create the file `terminal.desktop` in `~/.local/share/file-manager/actions/` (you may need to create the folders too).
- Add the following content:
  ```
  [Desktop Entry]
  Type=Action
  Tooltip=Open Terminal
  Name=Open Terminal
  Profiles=profile-one;
  Icon=utilities-terminal
  
  [X-Action-Profile profile-one]
  MimeTypes=inode/directory;
  Exec=alacritty --working-directory %f
  Name=Default profile
  ```
  > **Note**: In this example I am using *Alacritty*, to use a different terminal, change the line that starts with `Exec=` accordingly.
  
#### Screen tearing and picom
- Use [this video](https://www.youtube.com/watch?v=MfL_JkcEFbE) to determine if the screen is tearing.
- To avoid tearing install `picon` and run it on start. This is a compositor, by default it adds shadows and fading animations, these effects can be disabled in the config.
>**Issue**: Adding `picon &` to `~/.xinitrc` causes a gray screen to appear for a second before the window manager starts. This behavior disappears if the locker autostarts.

#### GTK and Qt themes
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
