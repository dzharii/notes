# NFS Server and Client on Raspberry Pi

[Setup a NFS Server and Client on the Raspberry Pi](https://sysadmins.co.za/setup-a-nfs-server-and-client-on-the-raspberry-pi/)

I am using NFS server to create a persistent volume for /mnt/usb on rpb02,  rpb03, rpb04.
Content mostly copied from [Setup a NFS Server and Client on the Raspberry Pi](https://sysadmins.co.za/setup-a-nfs-server-and-client-on-the-raspberry-pi/)

## NFS Servers

```sh
NFSSHARE_DIR=/mnt/usb/nfs-share

echo "Create NFS Share"
sudo mkdir -p "$NFSSHARE_DIR"

echo "ensure permissions"
sudo chown -R pi:pi $NFSSHARE_DIR

echo "Install NFS server"
sudo apt-get install nfs-kernel-server nfs-common rpcbind -y

echo "Append /etc/exports with a very experimental perl script"
echo '
use strict; use v5.010;

my $etc_exports = "/etc/exports";
my $nfs_folder = "'$NFSSHARE_DIR'";

chomp(my @lines = `cat $etc_exports`);
my $nfs_export_line = "$nfs_folder *(rw,sync,no_subtree_check)";

my @existing = grep { /$nfs_folder/ } @lines;
if (@existing) {
  print "nfs share already exists in exports";
} else {
  open(my $fd, ">>", $etc_exports) or die "$!";
  say $fd "$nfs_export_line";
}
say "exports - Done";' > configure_nfs_exports.pl

sudo perl ./configure_nfs_exports.pl

echo 'Export the config, enable the services on boot and start NFS:'

sudo exportfs -ra
showmount -e $(hostname)
sudo systemctl enable rpcbind
sudo systemctl enable nfs-kernel-server
sudo systemctl start rpcbind
sudo systemctl start nfs-kernel-server

echo 'fix masking issue'
echo 'see https://blog.ruanbekker.com/blog/2017/12/09/unmask-a-masked-service-in-systemd/'

sudo rm /lib/systemd/system/nfs-common.service
sudo systemctl daemon-reload

sudo systemctl enable nfs-common
sudo systemctl start nfs-common


```

Test:
```sh
mkdir test-nfs-share
sudo mount dbgpi:/mnt/usb/nfs-share/ /home/pi/test-nfs-share

```