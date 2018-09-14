# ls:

lsblk
lsusb

### Format USB drive to ext4
`sudo mkfs.ext4 /dev/sda1 -L untitled`

### Mount USB device
```
sudo mkdir -p /mnt/usb
sudo mount -t ext4 /dev/sda1 /mnt/usb
```

## Automount:

https://help.ubuntu.com/community/Fstab

```
sudo mkdir -p /mnt/usb
echo '/dev/sda1 /mnt/usb ext4 defaults 0 2' | sudo tee -a /etc/fstab
```







