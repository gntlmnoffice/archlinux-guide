
### Post installation

#### Connect to wifi

##### Using NetworkManager
- Start NetworkManager:
  ```
  sudo systemctl start NetworkManager.service
  ```
- Enable restarting the NetworkManager when the system reboots:
  ```
  sudo systemctl enable NetworkManager.service
  ```
- List nearby wifi networks:
  ```
  nmcli device wifi list
  ```
- Connect to a wifi network:
  ```
  nmcli device wifi connect SSID password password
  ```
- Use `nmtui` for a TUI similar to `wifi-menu` or `nm-connection-editor` for a GUI.
  
##### Using wifi-menu with netctl
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
  This will automatically start gnome when xorg starts

##### Install X
- Run `sudo pacman -Syu xorg-server xorg-xinit xorg-xrandr xorg-xsetroot` to install xorg
- Create the file `/etc/X11/Xwrapper.config` and add the following content to allow xinit to run from non-root users:
  ```
  allowed_users=anybody
  needs_root_rights=yes
  ```
- Run `xinit` to start the server, or add the following lines to `~/.bash_profile`:
  ```
  # Start X automatically
  if [[ ! $DISPLAY && $XDG_VTNR -eq 1 ]]; then
    startx
  fi
  ```
>Note: The last step is not needed if we use *GDM* and start it with ` sudo systemctl enable gdm.service`.

##### Set up display manager GDM
GDM can be installed with the `gdm` package, and it is installed as part of the `gnome` group.

- Install `gdm3setup`, an interface to configure GDM3, autologin options and change Shell theme:
  ```
  yay -S gdm3setup
  ```
- Enable the gdm service so it starts automatically:
  ```
  sudo systemctl enable gdm.service
  ```
- Put the initialization logic in `~/.xprofile`, with this approach you don't need`~/.xinitrc`, and the logic to start `X` from `~/.bash_profile` could be removed.

- Move your `~/.bash_profile` to `~/.profile`

>Note: With this setup for some reason I can't use `$TERMINAL` in `sxhkdrc`.

>Note: More [here](https://wiki.archlinux.org/index.php/GDM)

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
- Add support for NTFS:
  ```
  sudo pacman -Syu ntfs-3g
  ```
#### Install applications

  ```
  yay -S google-chrome
  sudo pacman -Syu firefox code sxhkd git openssh vlc blender krita cura 
  ```
  - Install [Plex](https://wiki.archlinux.org/index.php/Plex#Installation).
  - Install `gpick` for a color picker.
  - Install `rofi` to quickly launch apps. Run `rofi-theme-selector` to select the theme.
  - Install `file-roller` for an archive manager with GUI.
  - Install `openshot` for a video editor. 
  - Install `ranger` as a file manager, install `w3m` and add the line `set preview_images true` to the config file to show images, it works with *Alacritty*.
  - Install `gnome-screenshot` to take screenshots.
  - Install `dunst` to show notifications.
  - Install `zathura` and `zathura-pdf-poppler` for PDF and EPUB. More infor [here](https://wiki.archlinux.org/index.php/Zathura)
  
>Note: see the official [List of application](https://wiki.archlinux.org/index.php/List_of_applications) from the wiki for more.

