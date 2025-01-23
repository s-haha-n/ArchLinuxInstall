# ArchLinuxInstall

This is my personal guide for installing Arch Linux on my PC. It's tailored for setting up an optimized environment for Blender, Krita, and game development. The steps reflect my hardware setup and preferences, which include an NVIDIA GPU, AMD Ryzen CPU, and XFCE desktop environment.

## Connect to the Internet

    ip addr show

Run this command with WiFi password and network name set:

    iwctl --passphrase "WIFI_PASSWORD" station wlan0 connect NETWORK_NAME
> Type: __station wlan0 get-networks__ inside iwctl to get nearby WiFi networks.

## Run archinstall

- Language
- Mirrors
- Locales
- Disk configuration: use best-effort, select main drive. Filesystem: ext4
- Disk encryption
- Bootloader: systemd
- Unified Kernel Images
- Swap: default swap on zram
- Hostname/Root password/User account
- __Profile:__
	- Type: Desktop
	- Desktop Environment: LXQT
	- Graphics Driver: NVIDIA Proprietary
- Audio: Pipewire
- Kernels: (default and lts?)
- Network
- Timezone/automatic time sync: true

## Mirrors Updating (ldns)

### Update the Mirrorlist
If your downloads are slow, update your mirrors:

    sudo reflector --country "United States" --latest 10 --sort rate --save /etc/pacman.d/mirrorlist

### About ldns
`ldns` is a library designed to simplify DNS lookups and management. Ensures DNS resolution works smoothly, especially in situations where default setups might fail or become slow.

    sudo pacman -S ldns

This package was used to fix DNS resolution issues after enabling certain network configurations.

I think Warp broke mirrors and then this fixed it:

    sudo curl -o /etc/pacman.d/mirrorlist https://archlinux.org/mirrorlist/all/
    //figure out what happened lol

## Increase sudo Timeout

`sudo visudo` is to modify the default configuration file directly. Please consider adding local content in `/etc/sudoers.d/` instead of directly modifying this file. A better way is:

    cd /etc/sudoers.d
    sudo visudo -f user_name

Add the content:

    Defaults timestamp_timeout=(number) # Set it to 60

## Swap Caps Lock and Escape Keys

    sudo vim /etc/X11/xorg.conf.d/00-keyboard.conf

Appended section to end of it:

### Custom Section to Swap Caps Lock and Escape Keys
```
Section "InputClass"
    Identifier "swap caps and escape"     # A descriptive label for this specific configuration
    MatchIsKeyboard "on"                  # Ensures this applies to all keyboards
    Option "XkbOptions" "caps:swapescape" # Swaps Caps Lock and Escape
EndSection
```

### Enable Num Lock on Boot

    yay -S mkinitcpio-numlock
    sudo vim /etc/mkinitcpio.conf
   On line 4 for "HOOKS=" add 'numlock' after 'keymap' before 'modconf'.

## Bash and ZSH Setup

Make `.bash_history` sync up across all terminal sessions. It is usually only recording history from one session.

## Packages to Install

1. **qdirstat**: A graphical disk usage analyzer for viewing and managing storage space.

        sudo pacman -S qdirstat

2. **fsearch**: A fast file search utility.

        sudo pacman -S fsearch

3. **firefox**: A versatile web browser.

        sudo pacman -S firefox

4. **blender**: A 3D creation suite for modeling, sculpting, and animation.

        sudo pacman -S blender

5. **krita**: A powerful digital painting application.

        sudo pacman -S krita

6. **godot**: An open-source game engine for 2D and 3D development.

        sudo pacman -S godot


## Microcode and FSTRIM

### IMPORTANT MICROCODE AND FSTRIM FOR SSD BETTERNESS LOL

    sudo pacman -S amd-ucode # Installs AMD microcode
    # Added a line that uses the AMD .img
    vim /boot/loader/entries/2024-11-27_02-38-02_linux.conf

Installed utilities for using fstrim, then commands to set weekly trim:

    sudo pacman -S util-linux
    sudo systemctl enable fstrim.timer
    sudo systemctl start fstrim.timer
    sudo fstrim -v /

To check the status of `fstrim` on Arch Linux, you can use the following commands:

1. **Check if `fstrim.timer` is enabled**:

        sudo systemctl status fstrim.timer

    This should show whether the timer is active and enabled.

2. **Check the logs for `fstrim.service`**:

        sudo journalctl --unit=fstrim.service

    This will display the logs for the `fstrim` service, showing when it was last run and which filesystems were trimmed.

3. **Verify TRIM support on your SSD**:

        sudo lsblk --discard

    This command will list your block devices and show whether they support the discard (TRIM) operation. Look for non-zero values in the `DISC-GRAN` (discard granularity) and `DISC-MAX` (discard max bytes) columns.

4. **Check your `/etc/fstab` file**:

    Ensure that your SSD is listed with the `discard` option in your `/etc/fstab` file. For example:

        /dev/sda1  /  ext4  discard,errors=remount-ro  0  1

    This ensures that `fstrim` will be able to trim the filesystem.

### Handy Tips for First-Time Setup with fstrim
- Enabling `fstrim.timer` ensures your SSD remains optimized by periodically running the TRIM command.
- Check compatibility for TRIM operations on your SSD to avoid potential performance issues.
- Adjust the TRIM interval to suit your usage pattern if weekly is too frequent or infrequent.

## XORG Setup

Installed XORG and dependencies:

    sudo pacman -S xorg xorg-xinit

To set up `~/.xinitrc` to run KDE and update `.bash_profile`:

    sudo pacman -S --needed git base-devel

## Install Rust

    curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
    cargo install ripgrep
    # Also installed ripgrep and zoxide for 'z' instead of 'cd'

## Install OpenTabletDriver

Installed OpenTabletDriver through AUR:

    yay -S opentabletdriver

Needed this to have the `.json` file with the product ID changed. Commands for OpenTabletDriver:

    systemctl --user restart opentabletdriver
    systemctl --user enable opentabletdriver.service --now
    cd .local/share/OpenTabletDriver/Configurations/

# TODO

### Media Codecs
Research media codecs installation for playing various video formats, including HVEC. Add this to the top of the list for multimedia setups.


uninstalled warp manually used this to find traces:

    find /home/haha -name "*warp-terminal*" -exec ls -ld {} \;

idk what this is...

    inxi -Fxzc
    sudo pacman -S inxi
    inxi -Fxzc
    systemctl status NetworkManager

Checking SSH:

    systemctl status sshd
    systemctl start ssh

Setting root password:

    passwd
