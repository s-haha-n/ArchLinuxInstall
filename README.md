# ArchLinuxInstall

## Connect to the Internet

Personal guide for Arch Linux installation on my PC for Blender, Krita and game development.\
Booting off live USB. Connect to the internet run:

    ip addr show
    
**if you don't know network name** use 'iwctl' command.
inside 'iwctl' type: 

    station wlan0 get-networks
Once you know the network name run:

    iwctl --passphrase "WIFI_PASSWORD" station wlan0 connect NETWORK_NAME

Checking SSH:
    
    systemctl status sshd
    systemctl start ssh

Setting root password:

    passwd

## Run archinstall
