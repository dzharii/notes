# Deploy Kafka Mananager (Yahoo) to Raspberry PI

## Build kafka manager on local machine

Build kafka manager and create zip distro on your local machine.
See [yahoo/kafka-manager readme.md](https://github.com/yahoo/kafka-manager)

Inside zip: conf/application.conf, set the list of zookeeper hosts.

kafka-manager.zkhosts="my.zookeeper.host.com:2181", like
`kafka-manager.zkhosts="rp0:2181,rp1:2181,rp2:2181"`


Copy file to pi's home on RPI

```sh
KM_VER=1.3.3.21
echo "extract archive"
sudo unzip kafka-manager-${KM_VER}.zip -d /opt
sudo mv /opt/kafka-manager-${KM_VER} /opt/kafka-manager
```

You can test it by running
```sh
sudo /opt/kafka-manager/bin/kafka-manager

```
Open it at http://rp0:9000

Create system.d service and user

```sh
echo "Make a kafkamanager system user"
sudo adduser --system --no-create-home --disabled-password --disabled-login kafkamanager

echo "service pid dir"
sudo mkdir /var/run/kafka-manager

echo "ensure permissions"
sudo chown -R kafkamanager:nogroup /opt/kafka-manager
sudo chown -R kafkamanager:nogroup /var/run/kafka-manager

echo "create systemd service"
echo '[Unit]
Description=Kafka Manager
Requires=network.target remote-fs.target
After=network.target remote-fs.target kafka.service

[Service]
Type=simple
PIDFile=/var/run/kafka-manager/kafka-manager.pid
User=kafkamanager
Group=nogroup
ExecStart=/opt/kafka-manager/bin/kafka-manager -Dpidfile.path=/dev/null
ExecStop=/bin/kill -9 $MAINPID
Restart=on-failure
SyslogIdentifier=kafka-manager

[Install]
WantedBy=multi-user.target' | sudo tee /etc/systemd/system/kafka-manager.service

```

```sh
sudo systemctl start kafka-manager
```

start on boot
```sh
sudo systemctl enable kafka-manager


```