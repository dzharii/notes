# Zookeeper on Raspberry PI

* [What is the proper way to install ZooKeeper on Ubuntu 16.04 for both standalone and multi-node deployment?](https://askubuntu.com/questions/1022575/what-is-the-proper-way-to-install-zookeeper-on-ubuntu-16-04-for-both-standalone)
* [Introduction to Perl one-liners](http://www.catonmat.net/blog/introduction-to-perl-one-liners/)
* [append a text on the top of the file](https://stackoverflow.com/questions/6141088/append-a-text-on-the-top-of-the-file)


See [Zookeeper bin releases download site](https://www.apache.org/dyn/closer.cgi/zookeeper/)

```sh
ZK_RELEASE_VER=3.4.13
echo "Make a zookeeper system user"
sudo adduser --system --no-create-home --disabled-password --disabled-login zookeeper

echo "download the binary release"
wget "http://www-eu.apache.org/dist/zookeeper/zookeeper-${ZK_RELEASE_VER}/zookeeper-${ZK_RELEASE_VER}.tar.gz"

echo "create initial directories"
sudo mkdir /opt/zookeeper
sudo mkdir /var/lib/zookeeper
sudo mkdir /var/lib/zookeeper/logs
sudo mkdir /var/run/zookeeper

echo "extract archive"
sudo tar -xvzf zookeeper-${ZK_RELEASE_VER}.tar.gz --directory /opt/zookeeper --strip-components 1

echo "edit configuration file"
sudo cp /opt/zookeeper/conf/zoo_sample.cfg /opt/zookeeper/conf/zoo.cfg
echo "* * * edit  dataDir"
sudo perl -pi -e 's!^dataDir=.+$!dataDir=/var/lib/zookeeper!m' /opt/zookeeper/conf/zoo.cfg
cat /opt/zookeeper/conf/zoo.cfg | grep dataDir=
echo "update log dir setting"
sudo perl -pi -e 's!^(ZOOBINDIR=.+)$!ZOO_LOG_DIR="/var/lib/zookeeper/logs"\n$1!m' /opt/zookeeper/bin/zkEnv.sh

echo "ensure permissions"
sudo chown -R zookeeper:nogroup /opt/zookeeper
sudo chown -R zookeeper:nogroup /var/lib/zookeeper
sudo chown -R zookeeper:nogroup /var/run/zookeeper

echo '[Unit]
Description=Apache ZooKeeper
Requires=network.target remote-fs.target
After=network.target remote-fs.target

[Service]
Type=simple
PIDFile=/var/run/zookeeper/zookeeper.pid
User=zookeeper
Group=nogroup
ExecStart=/opt/zookeeper/bin/zkServer.sh start-foreground
ExecStop=/opt/zookeeper/bin/zkServer.sh stop
Restart=on-failure
SyslogIdentifier=zookeeper

[Install]
WantedBy=multi-user.target' | sudo tee /etc/systemd/system/zookeeper.service

sudo systemctl start zookeeper
```

rsync -avxP /opt/hadoop/ user@slave-01:/opt/hadoop/

