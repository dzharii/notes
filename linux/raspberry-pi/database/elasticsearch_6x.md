# Install elasticsearch 6.x on Raspberry PI

# Does not work yet | WIP

[Install Elasticsearch with Debian Package](https://www.elastic.co/guide/en/elasticsearch/reference/current/deb.html)

Installation and configuration

```sh
echo "Download and install the public signing key"
wget -qO - https://artifacts.elastic.co/GPG-KEY-elasticsearch | sudo apt-key add -

echo "Configure apt"
sudo apt-get install apt-transport-https -y
echo "deb https://artifacts.elastic.co/packages/6.x/apt stable main" | sudo tee -a /etc/apt/sources.list.d/elastic-6.x.list
sudo apt-get update && sudo apt-get install elasticsearch -y

sudo /bin/systemctl daemon-reload
sudo /bin/systemctl enable elasticsearch.service

echo "create data directory"
sudo mkdir -p /mnt/usb1/elasticsearch/data
sudo chown -R elasticsearch:elasticsearch /mnt/usb1/elasticsearch/data

echo "edit /etc/default/elasticsearch"
echo "set ES_HEAP_SIZE=256m"
sudo perl -pi -e 's!^[#\s]*ES_HEAP_SIZE=(.+)?$!ES_HEAP_SIZE=256m!m' /etc/default/elasticsearch

echo "set DATA_DIR=/mnt/usb1/elasticsearch/data"
sudo perl -pi -e 's!^[#\s]*DATA_DIR=(.+)?$!DATA_DIR=/mnt/usb1/elasticsearch/data!m' /etc/default/elasticsearch

echo "edit elasticsearch yaml"
sudo cp --no-clobber /etc/elasticsearch/elasticsearch.yml /etc/elasticsearch/elasticsearch.yml.bak
echo 'cluster.name: pisearch
node.name: "Franz Kafka"
node.master: true
node.data: true
index.number_of_shards: 1
index.number_of_replicas: 1
bootstrap.mlockall: false
network.bind_host: 0.0.0.0
network.publish_host: 0.0.0.0
discovery.zen.minimum_master_nodes: 1
discovery.zen.ping.timeout: 3s
discovery.zen.ping.multicast.enabled: false
discovery.zen.ping.unicast.hosts: ["192.168.1.121:9200"]
' | sudo tee /etc/elasticsearch/elasticsearch.yml

sudo chown -R elasticsearch:elasticsearch /etc/elasticsearch/elasticsearch.yml



sudo systemctl start elasticsearch.service
```

