---
title: "How to connect to the internet with wpa supplicant"
date: 2018-06-05T22:19:42-03:00
tags: [linux]
draft: true
comments: true
---

When installing a distro with a minimal installation, you have to connect to the internet via command line. I'm gonna show you how to connect with the simplest tool, without all those unecessarily programs, like NetworkManager, Connman, and other bloat things. After all, we are using a minimal installation, we don't want packages we don't need.

You will need these packages:

- wpa_supplicant
- dhcpcd

In some distributions they are installed by default. But, in case you don't have them, install with the commands below:

On Arch Linux:

    $ sudo pacman -S wpa_supplicant dhcpcd

On Ubuntu:

    $ sudo apt install wpa_supplicant dhcpcdp

On [Void Linux](https://voidlinux.org/):

    $ sudo xbps-install -S wpa_supplicant dhcpcd

Now you need to enable the wireless interface to connect, to see what's your interface,
run:

    $ ip link show

This command will probably show two or more network interfaces, the loopback, and wireless. The interface you need should be wlp6s0, wlp2s0 or something like that. To enable the interface, run:

    $ sudo ip link set up <interface>

If you don't know which network to connect, you can search it by every wifi close to your computer with this command:
    $ sudo iw dev <interface> scan | grep SSID

To connect to a normal wifi (WPA-PSK networks), you must generate the configuration file
with _wpa\_passphrase_.

    # wpa_passhprase <SSID> <PASSWORD> >> /etc/wpa_supplicant/wpa_supplicant-<device_name>.conf

Your file should be like this:

```bash
network={
    ssid="SSID"
    #psk="PASSWORD"
    psk=<some_numbers>
}
```
If you don't want your wifi passwords stored in plain text (although only root can view), you should delete the commented line.

Enable the wpa_supplicant to connect to the network with the following command:

    # wpa_supplicant -B -Dwext -i<interface> -c/etc/wpa_supplicant/wpa_supplicant-<interface>.conf 1>/etc/wpa_supplicant/wpa.log 2>&1 &

This command will make wpa_supplicant run in the background and redirect the output (stdout) and stderr to the file wpa.log, so if you can't connect, read the content of this file to know what's wrong.

Now you have to start the DHCP client to enable the internet.

    $ dhcpcd

Your internet is configured and ready to be used!

## Connect to a network with autentication
If the network you are trying to connect needs autentication (you have to connect with a login and password), only the above steps won't work.

Edit your `/etc/wpa_supplicant/wpa_supplicant-<device_name>.conf` file, and leave the file this way:

```bash
network={
    ssid="SSID"
    eap=LEAP
    identity="<YourLogin>"
    password="<YourPassword>"
}
```

## Connect automatically on the boot up

You may want to always connect automatically when you are close to the wifi, to do this, you
have to tell your system to enable the DHCP by default. You can do it with your [init](https://en.wikipedia.org/wiki/Init) system:

**Obs:** _If you don't know what is it, you are probably using **systemd**, which is present in almost every linux distribution, such as Ubuntu, Mint, and derivates._

If you are using [systemd](https://en.wikipedia.org/wiki/Systemd) as an init system:

    $ sudo systemctl enable dhcpcd.service

If you are using [Runit](https://wiki.voidlinux.eu/Runit) as an init system:

    # ln -s /etc/sv/dhcpcd /var/service/

You can have as much networks in the file as you want, when you get close to one of them, it will connect automatically.
