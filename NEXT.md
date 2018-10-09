
## Random Access TODO:
* [ X ] [Apache Hadoop and HDFS on Raspberry PI](linux/raspberry-pi/database/hadoop.md )
* [ X ] [Apache Zookeeper](linux/raspberry-pi/zookeeper/zookeeper.md)
* [ X ] [Apache Kafka](linux/raspberry-pi/kafka/kafka.md)
* [ X ] [Elasticsearch](linux/raspberry-pi/database/elasticsearch_6x.md)
* [ > ] [ZerroMQ](https://github.com/zeromq)
        https://github.com/MonsieurV/ZeroMQ-RPi
        https://www.pihomeserver.fr/en/2014/01/15/raspberry-pi-home-server-le-message-broker-zeromq-pour-lier-vos-machines/
        http://osdevlab.blogspot.com/2015/12/how-to-install-zeromq-package-in.html
* Minio
* Graphite
* [  ] [MariaDB/MySQL with Master and 2 replicas](linux/raspberry-pi/database/mysql-mariadb.md)
* [  ] MongoDB
* [  ] [rqlite](linux/raspberry-pi/database/rqlite.md)
        [rqlite/rqlite](https://github.com/rqlite/rqlite) - rqlite is an easy-to-use, lightweight, distributed relational database,
        which uses SQLite as its storage engine. Forming a cluster is very straightforward, it gracefully handles
        leader elections, and tolerates failures of machines,
        including the leader. rqlite is available for Linux, OSX, and Microsoft Windows.
* [ ] Graphite Cluster, see [The architecture of clustering Graphite](https://grey-boundary.io/the-architecture-of-clustering-graphite
      [Creating a Graphite Cluster on Kubernetes](https://engineering.nanit.com/creating-a-graphite-cluster-on-kubernetes-6b402a8a7438)


Inbox

RPI Voltage:
```

for id in core sdram_c sdram_i sdram_p ; do \
     echo -e "$id:\t$(vcgencmd measure_volts $id)" ; \
done

```