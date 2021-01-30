[Set Up Your Own WireGuard VPN Server on Ubuntu - LinuxBabe](https://www.linuxbabe.com/ubuntu/wireguard-vpn-server-ubuntu)
[WireguardNotes/serverNotes.txt at master · coding-flamingo/WireguardNotes](https://github.com/coding-flamingo/WireguardNotes/blob/master/serverNotes.txt)
[WireGuard - ArchWiki](https://wiki.archlinux.org/index.php/WireGuard)


sudo apt-get update
sudo apt-get install wireguard wireguard-tools wireguard-dkms  -y
sudo reboot


wg genkey | sudo tee /etc/wireguard/server_private.key | wg pubkey | sudo tee /etc/wireguard/server_public.key
WGUARD_PK=`sudo cat /etc/wireguard/server_private.key`
echo '
[Interface]
Address = 10.10.10.1/24
ListenPort = 51820
DNS = 8.8.8.8
PrivateKey = '"$WGUARD_PK"'

#replace eth0 with the interface open to the internet (e.g might be wlan0 if wifi or eth0 for ethernet or anything else you name your interface)
PostUp = iptables -A FORWARD -i %i -j ACCEPT; iptables -A FORWARD -o %i -j ACCEPT; iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE
PostDown = iptables -D FORWARD -i %i -j ACCEPT; iptables -D FORWARD -o %i -j ACCEPT; iptables -t nat -D POSTROUTING -o eth0 -j MASQUERADE

' | sudo tee /etc/wireguard/wg0.conf

sudo chmod 600 /etc/wireguard/ -R

sudo perl -pi -e 's!^#?net.ipv4.ip_forward=.+$!net.ipv4.ip_forward=1!m' /etc/sysctl.conf
sudo sysctl -p

sudo apt-get install bind9 -y

systemctl status bind9

echo '
options {
        directory "/var/cache/bind";

        // If there is a firewall between you and nameservers you want
        // to talk to, you may need to fix the firewall to allow multiple
        // ports to talk.  See http://www.kb.cert.org/vuls/id/800113

        // If your ISP provided one or more IP addresses for stable
        // nameservers, you probably want to use them as forwarders.
        // Uncomment the following block, and insert the addresses replacing
        // the all-0s placeholder.

        // forwarders {
        //      0.0.0.0;
        // };

        //========================================================================
        // If BIND logs error messages about the root key being expired,
        // you will need to update your keys.  See https://www.isc.org/bind-keys
        //========================================================================
        dnssec-validation auto;

        auth-nxdomain no;    # conform to RFC1035
        listen-on-v6 { any; };
        
        allow-recursion { 127.0.0.1; 10.10.10.0/24; };
};
' | sudo tee /etc/bind/named.conf.options

sudo systemctl restart bind9

# Start WireGuard

## Start
sudo wg-quick up /etc/wireguard/wg0.conf

## Service
sudo systemctl start wg-quick@wg0.service
sudo systemctl stop wg-quick@wg0.service
sudo systemctl status wg-quick@wg0.service
sudo systemctl enable wg-quick@wg0.service

## Stop
sudo wg-quick down /etc/wireguard/wg0.conf
# 


# Android peer

wg genkey | sudo tee /etc/wireguard/android_private.key | wg pubkey | sudo tee /etc/wireguard/android_public.key
AND_PRIV=`sudo cat /etc/wireguard/android_private.key`
AND_PUB=`sudo cat /etc/wireguard/android_public.key`
SER_PUB=`sudo cat /etc/wireguard/server_public.key`

echo "a priv:$AND_PRIV"
echo "a  pub:$AND_PUB"

echo '
[Interface]
Address = 10.10.10.2/24
DNS = 8.8.8.8
PrivateKey = '"$AND_PRIV"'

[Peer]
PublicKey = '"$SER_PUB"'
Endpoint = AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA.zharii.com:51820
AllowedIPs = 0.0.0.0/0, ::/0

' | sudo tee /etc/wireguard/android.conf


echo '

[Peer]
# phone
PublicKey = '"$AND_PUB"'
AllowedIPs = 10.10.10.2/24


' | sudo tee -a /etc/wireguard/wg0.conf

sudo apt-get install qrencode -y
sudo cat /etc/wireguard/android.conf | qrencode -t utf8



## Dynamic DNS
# https://support.google.com/domains/answer/6147083?hl=en
# [Resolve DNS with dynamic IP for google domain with ddclient. | by Jiashun Zheng | Medium](https://jiashun-zheng.medium.com/resolve-dns-with-dynamic-ip-for-google-domain-with-ddclient-7fc484120bc1)

GDOMAIN_USER=AAAAAA
GDOMAIN_PASS=AAAAAAAAAA
GDOMAIN=AAAAAAAAAAAAAA.zharii.com

DEBIAN_FRONTEND=noninteractive sudo apt-get install ddclient curl -y



echo '
ssl=yes
use=cmd
cmd='"'"'curl ipecho.net/plain'"'"'
protocol=googledomains
login='"$GDOMAIN_USER"'
password='"$GDOMAIN_PASS"'
'"$GDOMAIN"'
'| sudo tee /etc/ddclient.conf



echo '
run_dhclient="false"
run_ipup="false"
run_daemon="true"
daemon_interval="300"' | sudo tee /etc/default/ddclient


# Troubleshooting

sudo ddclient -daemon=0 -debug -verbose -noquiet

## Test server 

