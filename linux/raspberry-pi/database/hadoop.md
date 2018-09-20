

* [Building a Hadoop cluster with Raspberry Pi](https://developer.ibm.com/recipes/tutorials/building-a-hadoop-cluster-with-raspberry-pi/)


https://hadoop.apache.org/releases.html

```
cd /opt/hadoop/
sudo wget http://mirrors.gigenet.com/apache/hadoop/common/hadoop-3.1.1/hadoop-3.1.1.tar.gz
sudo tar xvf hadoop-3.1.1.tar.gz
sudo mv hadoop-3.1.1 hadoop

```


Master / Name node setup with HDFS on USB Drive

```
sudo mkdir -p /mnt/usb/hdfs/namenode

```

All datanodes

```
sudo mkdir -p /mnt/usb/hdfs/datanode

```

## /etc/hosts

```sh

echo '
127.0.0.1       localhost
::1             localhost ip6-localhost ip6-loopback
ff02::1         ip6-allnodes
ff02::2         ip6-allrouters

192.168.1.110         rp0
192.168.1.111         rp1
192.168.1.112         rp2
' | sudo tee /etc/hosts

```


Hadoop config example:

```sh

echo '<configuration>
    <property>
        <name>fs.default.name</name>
        <value>hdfs://rp0:9000/</value>
    </property>
    <property>
        <name>fs.default.FS</name>
        <value>hdfs://rp0:9000/</value>
    </property>
</configuration>
' | sudo tee $HADOOP_HOME/etc/hadoop/core-site.xml



echo '<configuration>
    <property>
        <name>dfs.datanode.data.dir</name>
        <value>/mnt/usb/hdfs/datanode</value>
        <final>true</final>
    </property>
    <property>
        <name>dfs.namenode.name.dir</name>
        <value>/mnt/usb/hdfs/namenode</value>
        <final>true</final>
    </property>
    <property>
        <name>dfs.namenode.http-address</name>
        <value>rp0:50070</value>
    </property>
    <property>
        <name>dfs.replication</name>
        <value>2</value>
    </property>
</configuration>

' | sudo tee $HADOOP_HOME/etc/hadoop/hdfs-site.xml


echo '<configuration>
    <property>
        <name>yarn.resourcemanager.resource-tracker.address</name>
        <value>rp0:8025</value>
    </property>
    <property>
        <name>yarn.resourcemanager.scheduler.address</name>
        <value>rp0:8035</value>
    </property>
    <property>
        <name>yarn.resourcemanager.address</name>
        <value>rp0:8050</value>
    </property>
</configuration>

' | sudo tee $HADOOP_HOME/etc/hadoop/yarn-site.xml


echo '<configuration>
    <property>
        <name>mapreduce.job.tracker</name>
        <value>rp0:5431</value>
    </property>
    <property>
        <name>mapred.framework.name</name>
        <value>yarn</value>
    </property>
</configuration>
' | sudo tee $HADOOP_HOME/etc/hadoop/mapred-site.xml

```


## Start cluster
```
cd $HADOOP_HOME/sbin/
./start-yarn.sh
./start-dfs.sh

```

## Fixes

```
sudo chown pi /mnt/usb/hdfs -R
sudo chmod 750 /mnt/usb/hdfs


```