# Programs
List of the applications and tools I use. For more apps, check the official [List of application](https://wiki.archlinux.org/index.php/List_of_applications) from the arch wiki.

In the list you'll find the name of the package that you need to install, and the command you need to run to start the application if different from the name of the package. If the package is only available from the AUR it will be specified.

**Table of Contents**
- [1. Applications](#1-applications)
- [2. Tools](#2-tools)

## 1. Applications
- [st](https://st.suckless.org/) terminal, suckless tool
- [neovim](https://wiki.archlinux.org/index.php/Neovim), run it with `nvim`.
- [vim-plug](https://github.com/junegunn/vim-plug)<sup>AUR</sup>, plugin manager for vim.
- [sxhkd](https://wiki.archlinux.org/index.php/Sxhkd) for a key binder.
- [arandr](https://wiki.archlinux.org/index.php/Multihead#Configuration_using_arandr) for GUI and [xorg-xrandr](https://wiki.archlinux.org/index.php/Xrandr) for terminal, run it with `xrandr`.
- [google-chrome](https://wiki.archlinux.org/index.php/Chromium) <sup>AUR</sup>
- [firefox](https://wiki.archlinux.org/index.php/Firefox)
- [code](https://wiki.archlinux.org/index.php/Visual_Studio_Code) for open source release of *Visual Studio Code*.
- [git](https://wiki.archlinux.org/index.php/Git), also [openssh]() to connect without entering password.
- [dmenu](https://tools.suckless.org/dmenu/) general purpose gui fuzzy finder, this is a suckless utility.
- [sxiv](https://wiki.archlinux.org/index.php/Sxiv), image and gif viewer.
- [maim](https://github.com/naelstrof/maim) to take screenshots.
- [flameshot](https://github.com/lupoDharkael/flameshot) to take screenshots with annotations. You run it with `flameshot gui`, and you configure it with `flameshot config`, you need to add `flameshot &` to `~/.xinitrc`.
- `gpick` for a color picker.
- `file-roller` for an archive manager with GUI.
- [zathura](https://wiki.archlinux.org/index.php/Zathura) and `zathura-pdf-poppler` for a PDF reader.
- [mpv](https://wiki.archlinux.org/index.php/Mpv) for a video player.
- `pinta` for a simple drawing and image editing tool, modelled after *Paint.NET*.
- `krita` for a raster graphics editor for digital painting and illustration.
- [gimp](https://wiki.archlinux.org/index.php/Gimp) for a raster image editor for image editing.
- [inkscape](https://wiki.archlinux.org/index.php/Inkscape) for a vector graphics editor.
- [blender](https://wiki.archlinux.org/index.php/Blender) for a 3d editor.
- `cura` for 3D printing slicer.
- `openscad` for 3D CAD script based modeller.
- `openshot` for a video editor.
- `transmission-gtk` and `transmission-ctl` for GUI and CLI versions of [Transmission](https://wiki.archlinux.org/index.php/Transmission) for a BitTorrent client.
- [plex-media-server](https://wiki.archlinux.org/index.php/Plex)<sup>AUR</sup> for *Plex*. See instructions to [setup](https://wiki.archlinux.org/index.php/Plex#Setup).
- For *PIA* see instructions [here](https://wiki.archlinux.org/index.php/Private_Internet_Access#Official_installation_script).
- [ranger](https://wiki.archlinux.org/index.php/Ranger) for a file manager, install `w3m` and add the line `set preview_images true` to the config file to show images, it works with *Alacritty*.
- [sdcv](https://wiki.archlinux.org/index.php/Sdcv) CLI dictionary. The dictionaries I use are in my Google Drive, they should be copied to `/usr/share/stardict/dic`
## 2. Tools
- [fzf](https://github.com/junegunn/fzf) terminal fuzzy finder utility.
- [xdotool](https://jlk.fjfi.cvut.cz/arch/manpages/man/xdotool.1) window management tool.
- [moreutils](https://joeyh.name/code/moreutils/) collection of unix tools, I use it for `ifne` and `mispipe`.
- [inotify-tools](https://github.com/inotify-tools/inotify-tools/wiki) for reacting to changes in the filesystem.
- [reflector](https://wiki.archlinux.org/index.php/Reflector) to update the pacman mirrorlist
- [mlocate](https://wiki.archlinux.org/index.php/Mlocate) utility to locate files in the system, run with `locate` and `sudo updatedb` to update the database.
