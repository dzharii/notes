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
my $nfs_export_line = "$nfs_folder *(rw,sync,no_root_squash,no_all_squash,no_subtree_check)";

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

```sh

# Mount on Windows
# https://mapr.com/docs/51/DevelopmentGuide/c-mounting-nfs-on-a-windows-client.html

mount -o nolock dbgpi:/mnt/usb/nfs-share/ z:

```

# NFS Troubleshooting Tips

Copied from: [NFS permission denied](https://unix.stackexchange.com/a/79175)

You need to run the command on the server after modifying the `/etc/exports` file:

    $ exportfs -a

Also when debugging connectivity issues with NFS you can run the command `showmount -e <nfs server>` to see what mounts a given server is exporting out.

### example
    $ showmount -e cobbler
    Export list for cobbler:
    /cobbler/isos 192.168.1.0/24

### services running on nfs clients
You need to make sure that you have the following services running so that the clients can communicate with the NFS server:

    $ chkconfig --list|grep rpc
    rpcbind        	0:off	1:off	2:on	3:on	4:on	5:on	6:off
    rpcgssd        	0:off	1:off	2:off	3:on	4:on	5:on	6:off
    rpcidmapd      	0:off	1:off	2:off	3:on	4:on	5:on	6:off
    rpcsvcgssd     	0:off	1:off	2:off	3:off	4:off	5:off	6:off

As well as this one:

    $ chkconfig --list|grep nfs
    nfs            	0:off	1:off	2:off	3:off	4:off	5:off	6:off
    nfslock        	0:off	1:off	2:off	3:on	4:on	5:on	6:off

### rpcinfo
With the above services running you should be able to check that the client can make remote procedure calls (rpc) to the NFS server like so:

    $ rpcinfo -p cobbler
       program vers proto   port  service
        100000    2   tcp    111  portmapper
        100000    2   udp    111  portmapper
        100024    1   udp    807  status
        100024    1   tcp    810  status
        100011    1   udp    718  rquotad
        100011    2   udp    718  rquotad
        100011    1   tcp    721  rquotad
        100011    2   tcp    721  rquotad
        100003    2   udp   2049  nfs
        100003    3   udp   2049  nfs
        100003    4   udp   2049  nfs
        100021    1   udp  60327  nlockmgr
        100021    3   udp  60327  nlockmgr
        100021    4   udp  60327  nlockmgr
        100003    2   tcp   2049  nfs
        100003    3   tcp   2049  nfs
        100003    4   tcp   2049  nfs
        100021    1   tcp  57752  nlockmgr
        100021    3   tcp  57752  nlockmgr
        100021    4   tcp  57752  nlockmgr
        100005    1   udp    750  mountd
        100005    1   tcp    753  mountd
        100005    2   udp    750  mountd
        100005    2   tcp    753  mountd
        100005    3   udp    750  mountd
        100005    3   tcp    753  mountd

### mounting and the kernel modules
I see what you wrote in an answer that you then deleted. You should've added that info to the question!

I can see where you were getting stumped now. I don't believe you're suppose to be mounting using:

    $ mount -t nfsd ...

that should be:

    $ mount t nfs ...

Try changing that. Also I see where you were ultimately getting stumped. You didn't have the nfs kernel module loaded.

    $ modprobe nfs

---