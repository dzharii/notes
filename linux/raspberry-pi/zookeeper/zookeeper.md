# Zookeeper on Raspberry PI

* [What is the proper way to install ZooKeeper on Ubuntu 16.04 for both standalone and multi-node deployment?](https://askubuntu.com/questions/1022575/what-is-the-proper-way-to-install-zookeeper-on-ubuntu-16-04-for-both-standalone)
* [ZooKeeper Getting Started Guide](https://zookeeper.apache.org/doc/r3.1.2/zookeeperStarted.html)
* [Running Kafka on Raspberry Pi Cluster](https://github.com/keiraqz/RaspPiDemo/blob/master/kafka_config/README.md)
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

```

## Edit zoo.cfg: add cluster server

Given rp0, rp1, rp2 IPs already defined in /etc/hosts

```
sudo perl -pi -e '$line = 1 if /server.1=/; print "server.1=rp0:2888:3888\nserver.2=rp1:2888:3888\nserver.3=rp2:2888:3888\n" if !$line&&eof' /opt/zookeeper/conf/zoo.cfg
```

```sh
sudo systemctl start zookeeper
```

## put myid into each node

``
# on rp0
echo "1" | sudo tee /var/lib/zookeeper/myid && sudo chown -R zookeeper:nogroup /var/lib/zookeeper

# on rp1
echo "2" | sudo tee /var/lib/zookeeper/myid && sudo chown -R zookeeper:nogroup /var/lib/zookeeper

# on rp2
echo "3" | sudo tee /var/lib/zookeeper/myid && sudo chown -R zookeeper:nogroup /var/lib/zookeeper
```

## Testing commands
```
# dump
# Lists the outstanding sessions and ephemeral nodes. This only works on the leader.
echo dump | nc rp0 2181

# envi
# Print details about serving environment
echo envi | nc rp0 2181

# kill
# Shuts down the server. This must be issued from the machine the ZooKeeper server is running on.
echo envi | nc rp0 2181

# reqs
# List outstanding requests
echo reqs | nc rp0 2181

# ruok
# Tests if server is running in a non-error state. The server will respond with imok if it is running. Otherwise it will not respond at all.
echo ruok | nc rp0 2181
# returns imok

# srst
# Reset statistics returned by stat command.
echo srst | nc rp0 2181

# stat
# Lists statistics about performance and connected clients.
echo stat | nc rp0 2181


```

```
# rsync -avxP /opt/hadoop/ user@slave-01:/opt/hadoop/
```

