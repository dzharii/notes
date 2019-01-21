echo '
network:
  version: 2
  renderer: networkd
  ethernets:
    eth0:
      dhcp4: no
      dhcp6: no
      addresses: [192.168.1.155/24]
      gateway4: 192.168.1.1
      nameservers:
        addresses: [192.168.1.1]
' | tee /etc/netplan/50-cloud-init.yaml

sudo netplan --debug apply