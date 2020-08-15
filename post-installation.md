# Post installation
Some post installation steps to get the setup to a functional state.

**Table of Contents**
- [1. Connect to wifi](#1-connect-to-wifi)
  - [1.1. Using NetworkManager](#11-using-networkmanager)
  - [1.2. Using wifi-menu with netctl](#12-using-wifi-menu-with-netctl)
- [2. Add user](#2-add-user)
- [3. Install X](#3-install-x)
- [4. Install main packages](#4-install-main-packages)

## 1. Connect to wifi

### 1.1. Using NetworkManager
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

### 1.2. Using wifi-menu with netctl
- Run `wifi-menu` and create a profile. Name it something memorable like **home**
- Run `netctl enable <name-of-your-profile>` and next time you boot it should connect to the wifi automatically.

## 2. Add user
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

## 3. Install X
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

## 4. Install main packages
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
