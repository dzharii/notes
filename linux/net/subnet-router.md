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

```sh
sudo cp /etc/default/isc-dhcp-server /etc/default/isc-dhcp-server.bak
echo `
INTERFACESv4="eth0"
INTERFACESv6="" | sudo tee /etc/default/isc-dhcp-server`

```

# Alternative

https://downey.io/blog/create-raspberry-pi-3-router-dhcp-server/

```sh
echo '
interface eth0
static ip_address=10.0.0.1/8
static domain_name_servers=8.8.8.8,8.8.4.4
nolink' | sudo tee -a /etc/dhcpcd.conf

```

```sh
sudo apt install dnsmasq -y
sudo mv /etc/dnsmasq.conf /etc/dnsmasq.conf.old

```


```sh

echo '
# Our DHCP service will be providing addresses over our eth0 adapter
interface=eth0

# We will listen on the static IP address we declared earlier
listen-address=10.0.0.1

# My cluster doesnt have that many Pis, but since well be using this as
# a jumpbox it is nice to give some wiggle-room.
# We also declare here that the IP addresses we lease out will be valid for
# 12 hours
dhcp-range=10.0.0.32,10.0.0.128,12h

# Decided to assign static IPs to the kube cluster members
# This would make it easier for tunneling, certs, etc.
# dhcp-host=b8:27:eb:00:00:01,10.0.0.50
# dhcp-host=b8:27:eb:00:00:02,10.0.0.51
# dhcp-host=b8:27:eb:00:00:03,10.0.0.52
# dhcp-host=b8:27:eb:00:00:04,10.0.0.53
# dhcp-host=b8:27:eb:00:00:05,10.0.0.54
# dhcp-host=b8:27:eb:00:00:06,10.0.0.55
# dhcp-host=b8:27:eb:00:00:07,10.0.0.56

# This is where you declare any name-servers. Well just use Googles
server=8.8.8.8
server=8.8.4.4

# Bind dnsmasq to the interfaces it is listening on (eth0)
bind-interfaces

# Never forward plain names (without a dot or domain part)
domain-needed

# Never forward addresses in the non-routed address spaces.
bogus-priv

# Use the hosts file on this machine
expand-hosts

# Useful for debugging issues
# log-queries
# log-dhcp
' | sudo tee /etc/dnsmasq.conf

```


```sh
echo '
#
# /etc/sysctl.conf - Configuration file for setting system variables
# See /etc/sysctl.d/ for additional system variables.
# See sysctl.conf (5) for information.
#

#kernel.domainname = example.com

# Uncomment the following to stop low-level messages on console
#kernel.printk = 3 4 1 3

##############################################################3
# Functions previously found in netbase
#

# Uncomment the next two lines to enable Spoof protection (reverse-path filter)
# Turn on Source Address Verification in all interfaces to
# prevent some spoofing attacks
#net.ipv4.conf.default.rp_filter=1
#net.ipv4.conf.all.rp_filter=1

# Uncomment the next line to enable TCP/IP SYN cookies
# See http://lwn.net/Articles/277146/
# Note: This may impact IPv6 TCP sessions too
#net.ipv4.tcp_syncookies=1

# Uncomment the next line to enable packet forwarding for IPv4
net.ipv4.ip_forward=1

# Uncomment the next line to enable packet forwarding for IPv6
#  Enabling this option disables Stateless Address Autoconfiguration
#  based on Router Advertisements for this host
#net.ipv6.conf.all.forwarding=1


###################################################################
# Additional settings - these settings can improve the network
# security of the host and prevent against some network attacks
# including spoofing attacks and man in the middle attacks through
# redirection. Some network environments, however, require that these
# settings are disabled so review and enable them as needed.
#
# Do not accept ICMP redirects (prevent MITM attacks)
#net.ipv4.conf.all.accept_redirects = 0
#net.ipv6.conf.all.accept_redirects = 0
# _or_
# Accept ICMP redirects only for gateways listed in our default
# gateway list (enabled by default)
# net.ipv4.conf.all.secure_redirects = 1
#
# Do not send ICMP redirects (we are not a router)
#net.ipv4.conf.all.send_redirects = 0
#
# Do not accept IP source route packets (we are not a router)
#net.ipv4.conf.all.accept_source_route = 0
#net.ipv6.conf.all.accept_source_route = 0
#
# Log Martian Packets
#net.ipv4.conf.all.log_martians = 1
#

###################################################################
# Magic system request Key
# 0=disable, 1=enable all
# Debian kernels have this set to 0 (disable the key)
# See https://www.kernel.org/doc/Documentation/sysrq.txt
# for what other values do
#kernel.sysrq=1

###################################################################
# Protected links
#
# Protects against creating or following links under certain conditions
# Debian kernels have both set to 1 (restricted)
# See https://www.kernel.org/doc/Documentation/sysctl/fs.txt
#fs.protected_hardlinks=0
#fs.protected_symlinks=0
vm.max_map_count=262144
' | sudo tee /etc/sysctl.conf

```

```sh
sudo iptables -t nat -A POSTROUTING -o wlan0 -j MASQUERADE
sudo iptables -A FORWARD -i wlan0 -o eth0 -m state --state RELATED,ESTABLISHED -j ACCEPT
sudo iptables -A FORWARD -i eth0 -o wlan0 -j ACCEPT

```


# TEST:

```sh
sudo iptables -L -n -v
```

```
Chain INPUT (policy ACCEPT 1780 packets, 164K bytes)
 pkts bytes target     prot opt in     out     source               destination

Chain FORWARD (policy ACCEPT 0 packets, 0 bytes)
 pkts bytes target     prot opt in     out     source               destination
   20  1520 ACCEPT     all  --  wlan0  eth0    0.0.0.0/0            0.0.0.0/0            state RELATED,ESTABLISHED
   20  1520 ACCEPT     all  --  eth0   wlan0   0.0.0.0/0            0.0.0.0/0

Chain OUTPUT (policy ACCEPT 871 packets, 104K bytes)
 pkts bytes target     prot opt in     out     source               destination
```


https://gist.github.com/alonisser/a2c19f5362c2091ac1e7

```sh
echo iptables-persistent iptables-persistent/autosave_v4 boolean true | sudo debconf-set-selections
sudo apt-get -y install iptables-persistent

```

cat /etc/iptables/rules.v4
