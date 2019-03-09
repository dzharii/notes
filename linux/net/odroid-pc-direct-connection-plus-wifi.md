# direct eth0 connection between PC and Odroid with Internet access via wifi adapter

/etc/network/interfaces

```sh
# interfaces(5) file used by ifup(8) and ifdown(8)
# Include files from /etc/network/interfaces.d:
source-directory /etc/network/interfaces.d

auto lo
iface lo inet loopback


allow-hotplug eth0
iface eth0 inet static
  address 169.254.1.10
  # gateway 169.254.1.1
  netmask 255.255.255.0

allow-hotplug wlan0
iface wlan0 inet dhcp
```