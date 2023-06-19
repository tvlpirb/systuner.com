---
title: "Wayland Setup on Arch Linux"
date: 2023-06-18T10:20:15+03:00
tags: ["setup","tutorial","arch-linux"]
categories: ["wayland"]
draft: false
#description: "Desc Text."
#author: "Me"
# author: ["Me", "You"] # multiple authors
showToc: true
TocOpen: true
UseHugoToc: true
hidemeta: false
comments: false
#canonicalURL: "https://canonical.url/to/page"
disableHLJS: true # to disable highlightjs
disableShare: false
#summary: ""
#hideSummary: false
searchHidden: false
ShowReadingTime: true
ShowBreadCrumbs: true
ShowPostNavLinks: true
ShowWordCount: true
ShowCodeCopyButtons: true
ShowRssButtonInSectionTermList: true
robotsNoIndex: false
cover:
    image: "/images/hypr-cat-2.webp" # image path/url
    alt: "Taster screenshot" # alt text
    caption: "<text>" # display caption under cover
    relative: false # when using page bundles set this to true
    hidden: true # only hide on current single page
editPost:
    URL: "https://github.com/tvlpirb/systuner.com/content"
    Text: "Suggest Changes" # edit text
    appendFilePath: true # to append file path to Edit link
# weight: 1
# aliases: ["/first"]

---

This is a guide for achieving a wayland setup. It aims to be as extensive as 
possible but I make the assumption that you posses the knowledge to fill in the gaps.

If you follow this guide, you should end up with the following basic software:
- Hyprland, tiling based window manager with fancy animations
- Waybar, a status bar
- Wofi, application launcher
- Kitty, terminal emulator
- Mako, notification daemon
- (Optional) Firefox, web browser
- (Optional) Emacs, text editor
- (Optional) Polkit, some authentication stuff

Here is a demo Setup
{{<youtube id="q2nevZKvLmQ">}}

I will leave it up to you the reader to fill in the gaps if you would like to replicate the setup above as is. Or you can 
make your own setup or grab one from [reddit.com](https://reddit.com/r/unixporn). 

To get mine you will need to install necessary fonts and get the configs at [https://github.com/tvlpirb/desktop-env/tree/master/themes/hypr-gruvbox/](https://github.com/tvlpirb/desktop-env/tree/master/themes/hypr-gruvbox/). You can checkout the README file in the theme directory to find out what additional packages are necessary. Feel free to contact me for help, but please
do your research first!

## Necessary Packages
If you have an NVIDIA GPU, you may need to consider using the AUR hyprland-nvidia package instead,
consult with your distro or compile it yourself if you're not using Arch. 

Some programs still only support X so we will also install xwayland, you can choose to ommit this.

```Shell
$ doas pacman -Syu mako firefox kitty wofi hyprland ttf-jetbrains-mono ttf-jetbrains-mono-nerd 
xorg-xwayland gtk4 qt5-wayland qt6-wayland qt5ct glfw-wayland 
# AUR Packages
$ paru -S waybar-hyprland-git nwg-look
```
## Desktop Environment
### Hyprland startup and config
#### Startup Scripts
To get applications to use Wayland create `/usr/local/bin/wayland_enablement.sh`
with the following contents. This should also fix some issues with Java. [See also](https://wiki.archlinux.org/title/wayland)
```Shell
#!/bin/sh
#export GDK_DPI_SCALE=1.25 # set if needed
export GDK_BACKEND=wayland
export QT_QPA_PLATFORMTHEME=qt5ct
export QT_QPA_PLATFORM=wayland
export MOZ_ENABLE_WAYLAND=1
#export QT_SCALE_FACTOR=1  # set if needed
export _JAVA_AWT_WM_NONREPARENTING=1
export _JAVA_OPTIONS='-Dawt.useSystemAAFontSettings=on'
```
Now create `/usr/local/bin/hypr-run` with the following contents.
```Shell
#!/bin/sh

# Session
export XDG_SESSION_TYPE=wayland
export XDG_SESSION_DESKTOP=Hyprland
export XDG_CURRENT_DESKTOP=Hyprland

source /usr/local/bin/wayland_enablement.sh

exec Hyprland
```
Make sure to give executable permissions to these, in future you can now start Hyprland with hypr-run from
tty or with your desired display manager.

#### Config
You can now copy the default config `/usr/share/hyprland/hyprland.conf` and copy it to 
`~/.config/hypr/hyprland.conf`. Make sure to edit the terminal emulator to kitty or whatever you want to use.

You can find my config at the following repo [github.com/tvlpirb/desktop-env](https://github.com/tvlpirb/desktop-env).
For this guide I am using the hypr-gruvbox theme.

### Kitty
You can use kitty as is or choose to replace it with whatever suits you. Perhaps Gnome terminal :P 

### Waybar 
To use waybar I would recommend stealing my config at [github.com/tvlpirb/desktop-env/tree/master/themes/hypr-gruvbox/waybar](https://github.com/tvlpirb/desktop-env/tree/master/themes/hypr-gruvbox/waybar)

### Mako 
Because what's a DE without notifications? To theme this lookup the documentation.
```Shell
$ systemctl --user enable mako
$ systemctl --user start mako 
```

### Wofi 
For this you will need to configure it inside your hyprland config, you can add the following to 
`~/.config/hypr/hyprland.conf`

```Shell
bind = SUPER,R,exec, wofi --show=drun --lines=10 --prompt="Search" --allow-images --allow-markup -i
```

### GTK3
To get this working select your theme and fonts with `nwg-look`, alternatively
look for the `import-gsettings` script in my repo and use `lxappearance` to configure
your `~/.config/gtk-3.0/settings.ini`

## Applications
### Emacs 
We will be installing the native-comp emacs with the new GTK additions.
```Shell
$ paru -Syu emacs-gcc-wayland-devel-bin
```

### Polkit 
```Shell
$ doas pacman -S polkit-gnome
```
And add to `~/.config/hypr/hyprland.conf` the following `exec = /usr/lib/polkit-gnome/polkit-gnome-authentication-agent-1`
