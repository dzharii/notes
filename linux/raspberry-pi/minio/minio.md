# Minio on a Raspberry Pi

* [Minio on a Raspberry Pi 3 with Raspbian (Debian Jessie 8.0)](https://goo.gl/BqjWHn)
* [Minio Quickstart Guide](https://github.com/minio/minio/blob/master/README.md)
* [Distributed Minio Quickstart Guide](https://github.com/minio/minio/blob/master/docs/distributed/README.md)

```sh

if [ ! -f minio ]; then
    echo "download minio"
    wget https://dl.minio.io/server/minio/release/linux-arm/minio
fi

chmod +x minio

echo "Make a minio system user"
sudo adduser --system --no-create-home --disabled-password --disabled-login minio

echo "create dirs"
sudo mkdir -p /opt/miniobin
sudo mkdir -p /var/run/minio
sudo mkdir -p /mnt/usb/minio/data
sudo mkdir -p /mnt/usb1/minio/data

sudo cp ./minio /opt/miniobin/

echo "ensure permissions"
sudo chown -R minio:nogroup /opt/miniobin
sudo chown -R minio:nogroup /var/run/minio
sudo chown -R minio:nogroup /mnt/usb/minio/data
sudo chown -R minio:nogroup /mnt/usb1/minio/data


echo "create systemd service"

echo '[Unit]
Description=Minio Service
Requires=network.target remote-fs.target
After=network.target remote-fs.target

[Service]
Type=simple
PIDFile=/var/run/minio/minio.pid
User=minio
Group=nogroup
ExecStart=/opt/miniobin/minio
ExecStop=/bin/kill -SIGTERM $MAINPID
Restart=on-failure
SyslogIdentifier=minio

[Install]
WantedBy=multi-user.target' | sudo tee /etc/systemd/system/minio.service

```


```




