From [How to check hard disk performance](https://askubuntu.com/questions/87035/how-to-check-hard-disk-performance)

Write:
```
dd if=/dev/zero of=/tmp/output bs=8k count=10k; rm -f /tmp/output
```

```
pi@rp1:~ $  sudo dd if=/dev/zero of=/mnt/usb/output bs=8k count=50k; sudo rm -f /mnt/usb/output
51200+0 records in
51200+0 records out
419430400 bytes (419 MB, 400 MiB) copied, 47.7687 s, 8.8 MB/s
pi@rp1:~ $  sudo dd if=/dev/zero of=/mnt/usb1/output bs=8k count=50k; sudo rm -f /mnt/usb1/output
51200+0 records in
51200+0 records out
419430400 bytes (419 MB, 400 MiB) copied, 34.692 s, 12.1 MB/s
```