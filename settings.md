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
>Note: This is only needed if a tiled window manager is not used.

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
- Add it to `~/.xinitrc` file before `exec`:
  ```
  numlockx &
  ```
>Note: More [here](https://wiki.archlinux.org/index.php/Activating_numlock_on_bootup#startx)

#### Auto hide cursor
- Run:
  ```
   sudo pacman -Syu unclutter
  ```
- Add it to `~/.xinitrc` file before `exec`:
  ```
  unclutter &
  ```
>Note: More [here](https://wiki.archlinux.org/index.php/Unclutter)

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
>Note: More [here](https://wiki.archlinux.org/index.php/Power_management/Suspend_and_hibernate#Hibernation).

#### Customize GRUB
- Install `grub-customizer` and run it with `sudo` to customize grub

#### Screen locker
- Install `xss-lock` to automatically start your favorite locker, I use `xsecurelock`.
- Add `xss-lock <your-locker> &` to `~/.xinitrc`

#### Disable action when lid closes
- Edit `/etc/systemd/logind.conf` and make set:
```
HandleLidSwitch=ignore
```
- Run the following command (it will restart your computer):
```
systemctl restart systemd-logind
```
>Note: More [here](https://wiki.archlinux.org/index.php/Power_management#Power_management_with_systemd)

#### Multiple monitors
- Install `arandr` a graphical interface for `xrandr` to manage multiple monitors, or use `xorg-xrandr` directly from the terminal instead.
