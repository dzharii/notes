[How-To: Turn a Raspberry Pi into a WiFi router](http://raspberrypihq.com/how-to-turn-a-raspberry-pi-into-a-wifi-router/)
[SETTING UP A RASPBERRY PI AS A DHCP SERVER](http://www.noveldevices.co.uk/rp-dhcp-server)
[Setting up a Raspberry Pi as an access point in a standalone network (NAT)](https://www.raspberrypi.org/documentation/configuration/wireless/access-point.md)

[DHCP DAEMON ON RASPBERRY PI](http://hawksites.newpaltz.edu/myerse/2018/06/19/dhcp-daemon-on-raspberry-pi/)
[How to: Make a Raspberry Pi Powered Wifi Repeater](https://pastebin.com/A4jUp2Nq)

[HOW TO SET UP A STATIC IP ON THE RASPBERRY PI](http://www.circuitbasics.com/how-to-set-up-a-static-ip-on-the-raspberry-pi/)

https://www.raspberrypi.org/forums/viewtopic.php?p=798866#p798866
https://www.raspberrypi.org/forums/viewtopic.php?t=191453


[baking a pi router for my raspberry pi kubernetes cluster](https://downey.io/blog/create-raspberry-pi-3-router-dhcp-server/)



nmap (Windows) local network scan

```
nmap -sP 192.168.1.1/24

```

Install DHCP software

```sh
sudo apt-get install isc-dhcp-server -y

```

```sh
sudo cp /etc/dhcp/dhcpd.conf /etc/dhcp/dhcpd.conf.bak
echo '# dhcpd.conf

default-lease-time 600;
max-lease-time 7200;

ddns-update-style none;

#authoritative;

subnet 192.168.10.0 netmask 255.255.255.0 {
 range 192.168.10.10 192.168.10.30;
 option broadcast-address 192.168.10.255;
 option routers 192.168.10.1;
 default-lease-time 600;
 max-lease-time 7200;
 option domain-name "local";
 option domain-name-servers 8.8.8.8, 8.8.4.4;
}
' | sudo tee /etc/dhcp/dhcpd.conf
```

```
cp /etc/default/isc-dhcp-server /etc/default/isc-dhcp-server.bak
echo `
INTERFACESv4="eth0"
INTERFACESv6="" | sudo tee /etc/default/isc-dhcp-server`

```

```
iface eth0 inet static
address <the-IP-address-of-your-Pi-that-will-be-the-DHCP-server>
netmask <the-subnet-mask-of-your-LAN>

gateway <the-IP-address-of-your-LAN-gateway>
```


