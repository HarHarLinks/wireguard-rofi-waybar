# wireguard-rofi-waybar

This project is intended as a plugin for [Waybar](https://github.com/Alexays/Waybar/) (and [rofi](https://github.com/davatorium/rofi)) users who want to manage their [WireGuard](https://www.wireguard.com/) connections with [NetworkManager](https://wiki.gnome.org/Projects/NetworkManager).

It consists of 2 components:
1. `wireguard.sh` is a shell interface to get active and available wireguard connections and toggle them.
2. `wireguard-rofi.sh` is a scripted rofi custom mode (see `man 5 rofi-script`)

It should be easy to adapt it for other bars than Waybar or similar as well. You can also simply use only either the indicator part in Waybar or the rofi menu.

# Usage

When you're connected to wireguard using NetworkManager, a segment showing the VPN's name and your IP will appear in your Waybar. You can click this segment to open a rofi menu that allows you to connect or disconnect to and from any WireGuard connection available through NetworkManager.
The rofi menu is color coded by connection status (dis/connected). When toggling an action, a notification is sent to your notification daemon by `notify-send`, if you have it installed.
You can also bind a hotkey to call the rofi menu without using the mouse.

Follow the [installation guide](#Installation) to set this up.

For information on how to use `wireguard.sh`'s script mode, take a look at its comment header.

# Requirements

- [bash](https://www.gnu.org/software/bash/)
- [NetworkManager](https://wiki.gnome.org/Projects/NetworkManager) (specifically `nmcli`)
- for the indicator: [Waybar](https://github.com/Alexays/Waybar/)
- for the toggle menu: [Rofi](https://github.com/davatorium/rofi) or since you're likely using Wayland, [its fork with Wayland support](https://github.com/lbonn/rofi)
- optionally, for notifications: `notify-send` ([libnotify](https://developer.gnome.org/notification-spec/)) and a notification daemon

I'm personally using it on Archlinux with the following packages:

- core/bash
- extra/networkmanager
- community/waybar
- aur/rofi-lbonn-wayland-git
- extra/libnotify
- (community/sway)

# Installation

To install this, simply put wireguard.sh and wireguard-rofi.sh somewhere waybar can read them and make sure they're executable (`chmod 755` or similar).
Your user account also needs access rights to NetworkManager, or `nmcli` to be precise.

I use this repo as a submodule to my dotfiles at `~/.config/waybar/wireguard-rofi-waybar`. Just take care to adjust the paths appropriately in your config.

## Configure Waybar

This will make a custom segment available to your Waybar. Leave the `"on-click"` line out if not using rofi. The `pkill` command and `"signal"` are used to instantly update the display after calling the rofi menu by clicking on the Waybar segment, so modify both occurences of the value (`6`) to a signal that is still unused in your Waybar config.

Tip: You can add rofi arguments the `"exec"` setting to 

```
    "custom/wireguard": {
        "format": "{}<big> 嬨</big>",
        "exec": "~/.config/waybar/wireguard-rofi-waybar/wireguard.sh",
        "on-click": "rofi -modi 'WireGuard:~/.config/waybar/wireguard-rofi-waybar/wireguard-rofi.sh' -show WireGuard; pkill -SIGRTMIN+6 waybar",
        "signal": 6,
        "interval": 60,
        "tooltip": false
    }
```

## Add hotkey to sway

I personally use the [sway](https://swaywm.org/) tiled window manager. You can bind a key combo by using a similar command to the `"exec"` setting in your Waybar config.

```
# wireguard
bindsym $mod+Shift+w exec "rofi -modi 'WireGuard:~/.config/waybar/wireguard-rofi-waybar/wireguard-rofi.sh' -show WireGuard; pkill -SIGRTMIN+6 waybar"
```

# Contributions

... and suggestions are welcome, just open an issue or pull request.
