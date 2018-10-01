# Install elasticsearch 6.x on Raspberry PI

# Does not work yet | WIP

* [Install Elasticsearch with Debian Package](https://www.elastic.co/guide/en/elasticsearch/reference/current/deb.html)
* [Elasticsearch Reference [6.4] » Set up Elasticsearch » Important System Configuration » Virtual memory](https://www.elastic.co/guide/en/elasticsearch/reference/current/vm-max-map-count.html)
* [System call filter check](https://www.elastic.co/guide/en/elasticsearch/reference/master/_system_call_filter_check.html)
* [How to set ulimits on service with systemd?](https://unix.stackexchange.com/questions/345595/how-to-set-ulimits-on-service-with-systemd)
* [Increase max_map_count Kernel Parameter (Linux)](http://docs.actian.com/vector/4.2/index.html#page/User/Increase_max_map_count_Kernel_Parameter_(Linux).htm)
* [How change default number of shards](https://discuss.elastic.co/t/how-change-default-number-of-shards/117985)

Installation and configuration

```sh
ES_VER=6.4.1

echo "Make a elasticsearch system user"
sudo adduser --system --no-create-home --disabled-password --disabled-login elasticsearch

echo "download the binary release"
wget "https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-${ES_VER}.tar.gz"

echo "create initial directories"
sudo mkdir -p /opt/elasticsearch
sudo mkdir -p /mnt/usb1/elasticsearch/data
sudo mkdir -p /var/run/elasticsearch
sudo mkdir -p /var/log/elasticsearch

echo "extract archive"
sudo tar -xvzf elasticsearch-${ES_VER}.tar.gz --directory /opt/elasticsearch --strip-components 1

echo "edit elasticsearch yaml"
sudo cp --no-clobber /opt/elasticsearch/config/elasticsearch.yml /opt/elasticsearch/config/elasticsearch.yml.bak
echo 'cluster.name: pisearch
node.name: "node-'$(hostname)'"
node.master: false
node.data: true
network.bind_host: 0.0.0.0
network.publish_host: 0.0.0.0
discovery.zen.minimum_master_nodes: 1
discovery.zen.ping.unicast.hosts: ["rp0"]
xpack.ml.enabled: false
bootstrap.system_call_filter: false
path.data: /mnt/usb1/elasticsearch/data
path.logs: /var/log/elasticsearch
' | sudo tee /opt/elasticsearch/config/elasticsearch.yml

sudo cp --no-clobber /opt/elasticsearch/config/jvm.options /opt/elasticsearch/config/jvm.options.bak
echo '## JVM configuration
-server

# Xms represents the initial size of total heap space
# Xmx represents the maximum size of total heap space

-Xms256m
-Xmx256m

## GC configuration
-XX:+UseConcMarkSweepGC
-XX:CMSInitiatingOccupancyFraction=75
-XX:+UseCMSInitiatingOccupancyOnly

## optimizations

# pre-touch memory pages used by the JVM during initialization
-XX:+AlwaysPreTouch

## basic

# explicitly set the stack size
-Xss1m

# set to headless, just in case
-Djava.awt.headless=true

# ensure UTF-8 encoding by default (e.g. filenames)
-Dfile.encoding=UTF-8

# use our provided JNA always versus the system one
-Djna.nosys=true

# turn off a JDK optimization that throws away stack traces for common
# exceptions because stack traces are important for debugging
-XX:-OmitStackTraceInFastThrow

# flags to configure Netty
-Dio.netty.noUnsafe=true
-Dio.netty.noKeySetOptimization=true
-Dio.netty.recycler.maxCapacityPerThread=0

# log4j 2
-Dlog4j.shutdownHookEnabled=false
-Dlog4j2.disable.jmx=true

-Djava.io.tmpdir=${ES_TMPDIR}

## heap dumps

# generate a heap dump when an allocation from the Java heap fails
# heap dumps are created in the working directory of the JVM
-XX:+HeapDumpOnOutOfMemoryError

# specify an alternative path for heap dumps; ensure the directory exists and
# has sufficient space
-XX:HeapDumpPath=data

# specify an alternative path for JVM fatal error logs
-XX:ErrorFile=logs/hs_err_pid%p.log

## JDK 8 GC logging

8:-XX:+PrintGCDetails
8:-XX:+PrintGCDateStamps
8:-XX:+PrintTenuringDistribution
8:-XX:+PrintGCApplicationStoppedTime
8:-Xloggc:logs/gc.log
8:-XX:+UseGCLogFileRotation
8:-XX:NumberOfGCLogFiles=32
8:-XX:GCLogFileSize=64m
' | sudo tee /opt/elasticsearch/config/jvm.options

echo "ensure permissions"
sudo chown -R elasticsearch:nogroup  /opt/elasticsearch
sudo chown -R elasticsearch:nogroup  /var/run/elasticsearch
sudo chown -R elasticsearch:nogroup  /mnt/usb1/elasticsearch/data
sudo chown -R elasticsearch:nogroup  /var/log/elasticsearch

echo "create systemd service"
echo '[Unit]
Description=Elasticsearch 6x
Requires=network.target remote-fs.target
After=network.target remote-fs.target

[Service]
LimitAS=infinity
LimitRSS=infinity
LimitCORE=infinity
LimitNOFILE=65536
Type=simple
PIDFile=/var/run/elasticsearch/elasticsearch.pid
User=elasticsearch
Group=nogroup
ExecStart=/opt/elasticsearch/bin/elasticsearch
ExecStop=/bin/kill -SIGTERM $MAINPID
Restart=on-failure
SyslogIdentifier=elasticsearch

[Install]
WantedBy=multi-user.target' | sudo tee /etc/systemd/system/elasticsearch.service
sudo systemctl daemon-reload

echo "update system limits"
sudo sysctl -w vm.max_map_count=262144
sudo sysctl -p
sudo cp --no-clobber /etc/sysctl.conf /etc/sysctl.conf.bak
if ! grep -q "vm.max_map_count=" /etc/sysctl.conf; then
   echo 'vm.max_map_count=262144' | sudo tee -a /etc/sysctl.conf
fi
cat /proc/sys/vm/max_map_count



```

Run ONLY on master node (rp0)

```sh
sudo perl -pi -e 's!^node.master:.+$!node.master: true!m' /opt/elasticsearch/config/elasticsearch.yml
sudo cat /opt/elasticsearch/config/elasticsearch.yml | grep "node.master"

```

```sh
sudo /bin/systemctl enable elasticsearch
sudo systemctl start elasticsearch

```

```sh

tail -f /var/log/elasticsearch/pisearch.log

```


```sh
curl -XPUT 'http://rp0:9200/_all/_settings?preserve_existing=true' -H "Content-Type: application/json" -d '{
  "index.number_of_replicas" : "1",
  "index.number_of_shards" : "1"
}'
```

