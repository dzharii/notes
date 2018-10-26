# Kafka on Raspberry PI

* [Set up a Kafka cluster with Raspberry Pi](https://medium.com/@oliver_hu/set-up-a-kafka-cluster-with-raspberry-pi-2859005a9bed)
* [How to Set Up Kafka](https://dzone.com/articles/kafka-setup)
* [Running Kafka on Raspberry Pi Cluster](https://github.com/keiraqz/RaspPiDemo/blob/master/kafka_config/README.md)
* [kafka installation and systemd](https://gist.github.com/vipmax/9ceeaa02932ba276fa810c923dbcbd4f)
* [KAFKA V0.11 DISTRIBUTED CLUSTER CONFIG](https://bigdatagurus.wordpress.com/2017/07/28/kafka-which-knobs-to-turn/)

Install zookeeper first: [Zookeeper on Raspberry PI](../zookeeper/zookeeper.md)

Update `BROKERID=1` for each rpi

```sh
KF_VER=2.12-2.0.0
BROKERID=1
echo "Make a kafka system user"
sudo adduser --system --no-create-home --disabled-password --disabled-login kafka

echo "download the binary release"
wget "http://apache.cs.utah.edu/kafka/2.0.0/kafka_${KF_VER}.tgz"

echo "create initial directories"
sudo mkdir -p /opt/kafka
sudo mkdir -p /var/run/kafka
sudo mkdir -p /mnt/usb/kafka/data


echo "extract archive"
sudo tar -xvzf kafka_${KF_VER}.tgz --directory /opt/kafka --strip-components 1

echo "edit config"
sudo cp --no-clobber /opt/kafka/config/server.properties /opt/kafka/config/server.properties.bak

# broker.id
sudo perl -i -e'$BROKERID = shift; while (<>) { s!^broker.id=(?:.+)?$!broker.id=$BROKERID!m; print }' "$BROKERID" /opt/kafka/config/server.properties

# log.dirs=
sudo perl -i -e'$LOGDIRS = shift; while (<>) { s!^log.dirs=(?:.+)?$!log.dirs=$LOGDIRS!m; print }' "/mnt/usb/kafka/data" /opt/kafka/config/server.properties

# zookeeper.connect=rp0:2181,rp1:2181,rp2:2181
sudo perl -i -e'$ZKCON = shift; while (<>) { s!^zookeeper.connect=(?:.+)?$!zookeeper.connect=$ZKCON!m; print }' "rp0:2181,rp1:2181,rp2:2181" /opt/kafka/config/server.properties

echo "Update the bin/kafka-server-start.sh"
sudo perl -i -e'$EXPVALUE = "export JMX_PORT=\${JMX_PORT:-9999}\nexport KAFKA_HEAP_OPTS=\"-Xmx256M -Xms128M\"\n"; while (<>) { s!^(# limitations under the License.*)$!$1\n$EXPVALUE!m; print }' /opt/kafka/bin/kafka-server-start.sh

echo "Update bin/kafka-run-class.sh"
sudo perl -i -e'$EXPVALUE = "\nKAFKA_JVM_PERFORMANCE_OPTS=\"-client -XX:+UseParNewGC -XX:+UseConcMarkSweepGC -XX:+CMSClassUnloadingEnabled -XX:+CMSScavengeBeforeRemark -XX:+DisableExplicitGC -Djava.awt.headless=true\"\n"; while (<>) { s!^(# limitations under the License.*)$!$1\n$EXPVALUE!m; print }' /opt/kafka/bin/kafka-run-class.sh

echo "ensure permissions"
sudo chown -R kafka:nogroup /opt/kafka
sudo chown -R kafka:nogroup /var/run/kafka
sudo chown -R kafka:nogroup /mnt/usb/kafka/data


echo "create systemd service"
echo '[Unit]
Description=Apache Kafka
Requires=network.target remote-fs.target
After=network.target remote-fs.target zookeeper.service

[Service]
Type=simple
PIDFile=/var/run/kafka/kafka.pid
User=kafka
Group=nogroup
ExecStart=/opt/kafka/bin/kafka-server-start.sh /opt/kafka/config/server.properties
ExecStop=/opt/kafka/bin/kafka-server-stop.sh
Restart=on-failure
SyslogIdentifier=kafka

[Install]
WantedBy=multi-user.target' | sudo tee /etc/systemd/system/kafka.service

```

```sh
sudo systemctl start kafka
```

start on boot
```sh
sudo systemctl enable zookeeper
sudo systemctl enable kafka

```

## Testing

Start producer on rp0

```
cd /opt/kafka
./bin/kafka-console-producer.sh --broker-list rp0:9092  -pic v-topic
```

Start consumer on rp1

```
cd /opt/kafka
./bin/kafka-console-consumer.sh --bootstrap-server rp0:9092 --topic v-topic --from-beginning
```

write something in producer console, hit Enter and check consumer