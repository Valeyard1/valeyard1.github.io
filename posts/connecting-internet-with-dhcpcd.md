# How to connect to the internet with wpa\_supplicant and dhcpcd

When installing a distro with a minimal installation, you have to connect to
the internet via command line. I'm gonna show how to connect with the simplest
tool to do it.

You need the necessary packages to do it. However, they are in almost distribution.

- wpa\_supplicant
- dhcpcd

On Arch Linux:

    $ sudo pacman -S wpa_supplicant dhcpcd

On Ubuntu:

    $ sudo apt install wpa_supplicant dhcpcdp

On [Void Linux](https://voidlinux.org/):

    $ sudo xbps-install -S wpa_supplicant dhcpcd

Now you need to enable the wireless interface to connect, to see what's your interface,
run:

    $ ip link show

The interface should be `wlp6s0`, `wlp2s0` or something like that. To enable the interface, run:

    $ sudo ip link set up <interface>


To connect to a normal wifi (WPA-PSK networks), we must generate the configuration file
with _wpa\_passphrase_.

    # wpa_passhprase <SSID> <PASSWORD> >> /etc/wpa_supplicant/wpa_supplicant-<device_name>.conf

Your file should be like this:

    network={
        ssid="SSID"
        #psk="PASSWORD"
        psk=<some_numbers>
    }

Now you have to start the DHCP client to enable the internet.

    $ dhcpcd

Your internet is configured and ready to use!

## Connect automatically on the boot up

You may want to always connect automatically when you are close to the wifi, to do this, you
have to tell your system to enable the DHCP by default. You do it with your [init](https://en.wikipedia.org/wiki/Init) system:

*Obs:* _If you don't know what is it, you are probably using _systemd_, which is present in almost
every linux distribution, such as Ubuntu, Mint, and derivates._

If you are using [systemd](https://en.wikipedia.org/wiki/Systemd) as an init system:

    $ sudo systemctl enable dhcpcd.service

If you are using [Runit](https://wiki.voidlinux.eu/Runit) as an init system:

    # ln -s /etc/sv/dhcpcd /var/service/

$(date): 05/07/2018
