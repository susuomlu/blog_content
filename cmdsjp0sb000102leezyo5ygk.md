---
title: "Elasticsearch 9.x.x Installation and Cluster Setup"
datePublished: Fri Aug 01 2025 08:11:53 GMT+0000 (Coordinated Universal Time)
cuid: cmdsjp0sb000102leezyo5ygk
slug: elasticsearch-9xx-installation-and-cluster-setup
cover: https://cdn.hashnode.com/res/hashnode/image/stock/unsplash/aiqKc07b5PA/upload/6deb96f212f69e3d9a29d09ff47aeaab.jpeg
tags: kibana, elasticsearch, siem

---

Elasticsearch is a real-time, distributed search and analytics engine—a powerful open-source tool designed for efficiently storing, searching, and analyzing large volumes of data.

# **Elasticsearch Installation**

## **Installation Environment and Elasticsearch Version**

* OS: Ubuntu 24.04 LTS
    
* Elasticsearch: 9.1.0
    

For cluster configuration, prepare three virtual machines (VMs) as follows:

| **No.** | **host name** | **IP** |
| --- | --- | --- |
| #1 | es-node1 | 192.168.234.128 |
| #2 | es-node2 | 192.168.234.129 |
| #3 | es-node3 | 192.168.234.130 |

## **Download and Install Elasticsearch**

The Debian package for Elasticsearch 9.1.0 can be downloaded from the website and installed as follows:

```sh
wget https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-9.1.0-amd64.deb
wget https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-9.1.0-amd64.deb.sha512
shasum -a 512 -c elasticsearch-9.1.0-amd64.deb.sha512
sudo dpkg -i elasticsearch-9.1.0-amd64.deb
```

# **Elasticsearch Cluster Configuration**

## **Generate & Deploy Certificates**

To secure inter-node communication, generate a common SSL/TLS certificates and deploy them to each node:

```plaintext
sudo /usr/share/elasticsearch/bin/elasticsearch-certutil ca
sudo /usr/share/elasticsearch/bin/elasticsearch-certutil cert --ca elastic-stack-ca.p12
```

Copy the generated `elastic-certificates.p12` file to the `/etc/elasticsearch/certs/` directory on each node:

```plaintext
sudo scp elastic-certificates.p12 root@192.168.234.129:/etc/elasticsearch/certs
sudo scp elastic-certificates.p12 root@192.168.234.130:/etc/elasticsearch/certs
```

## **Configure elasticsearch.yml**

Assign a unique [`node.name`](http://node.name) for each node and add the necessary cluster settings:

```plaintext
sudo vim /etc/elasticsearch/elasticsearch.yml
```

**Configure on es-node1 / es-node2 / es-node3**

```plaintext
cluster.name: es-cluster
node.name: node-1 #Change the name on each node
network.host: 0.0.0.0

path.data: /opt/elasticsearch/data #the path ur choosing
path.logs: /opt/elasticsearch/logs #the path ur choosing

# List of cluster node IPs
discovery.seed_hosts: ["192.168.234.128", "192.168.234.129","192.168.234.130"]

# Specify master-eligible nodes for initial cluster formation (remove or comment out after initial setup)
cluster.initial_master_nodes: ["node-1", "node-2","node-3"]

# SSL/TLS settings
xpack.security.transport.ssl.enabled: true
xpack.security.transport.ssl.verification_mode: certificate
xpack.security.transport.ssl.client_authentication: required
xpack.security.transport.ssl.keystore.path: elastic-certificates.p12
xpack.security.transport.ssl.truststore.path: elastic-certificates.p12
```

**Note:** The `cluster.initial_master_nodes` setting is only necessary during the initial cluster formation. After the cluster is established, this setting should be removed or commented out. (Refer to [Bootstrapping a cluster](https://www.elastic.co/guide/en/elasticsearch/reference/current/modules-discovery-bootstrap-cluster.html))

# **Start Cluster and Verify**

Start the Elasticsearch service on each node and then verify the cluster status.

Start the service:

```plaintext
sudo systemctl start elasticsearch
```

Reset the password for the `elastic` account:

```plaintext
sudo /usr/share/elasticsearch/bin/elasticsearch-reset-password -u elastic -i
```

Check the node status:

```plaintext
curl -u elastic:your_pass http://192.168.234.128:9200/_cat/nodes?v
==============================================================================
ip              heap.percent ram.percent cpu load_1m load_5m load_15m node.role   master name
192.168.234.130           19          89  88    1.30    0.80     0.37 cdfhilmrstw -      node-3
192.168.234.129           24          89   9    0.29    0.17     0.13 cdfhilmrstw -      node-2
192.168.234.128           10          90  17    0.00    0.00     0.00 cdfhilmrstw *      node-1
```

Check the cluster health:

```plaintext
curl -u elastic:your_pass http://192.168.234.128:9200/_cluster/health?pretty
==============================================================================
{
  "cluster_name" : "es-cluster",
  "status" : "green",
  "timed_out" : false,
  "number_of_nodes" : 3,
  "number_of_data_nodes" : 3,
  "active_primary_shards" : 3,
  "active_shards" : 6,
  "relocating_shards" : 0,
  "initializing_shards" : 0,
  "unassigned_shards" : 0,
  "unassigned_primary_shards" : 0,
  "delayed_unassigned_shards" : 0,
  "number_of_pending_tasks" : 0,
  "number_of_in_flight_fetch" : 0,
  "task_max_waiting_in_queue_millis" : 0,
  "active_shards_percent_as_number" : 100.0
}
```

# **Kibana Integration**

For security reasons, the `elastic` account cannot be used with Kibana; instead, the built-in `kibana_system` account is utilized.

Reset the password for the `kibana_system` account:

```plaintext
sudo /usr/share/elasticsearch/bin/elasticsearch-reset-password -u kibana_system -i
```

## Download and install Kibana

The Debian package for Kibana 9.1.0 can be downloaded from the website and installed as follows:

```sh
wget https://artifacts.elastic.co/downloads/kibana/kibana-9.1.0-amd64.deb
shasum -a 512 kibana-9.1.0-amd64.deb
sudo dpkg -i kibana-9.1.0-amd64.deb
```

## **Configure kibana.yml**

**kibana.yml**

```plaintext
server.port: 5601
server.host: "0.0.0.0"
elasticsearch.hosts: ["http://192.168.234.128:9200","http://192.168.234.129:9200","http://192.168.234.130:9200"]
elasticsearch.username: "kibana_system"
elasticsearch.password: "your_pass"
```

#### **Kibana Startup**

```plaintext
cd /opt/kibana
nohup bin/kibana &
```

Now, access [http://192.168.234.128:5601](http://192.168.234.128:5601) [](http://192.168.234.128:5601/)(or the IP address of the node where Kibana is installed) in a web browser and log in with the elastic account.

# Conclusion

I introduced a simple way to install Elasticsearch and Kibana and set up a cluster.

The archive installation method is easy to install and manage, making it useful in various environments. Hope you find it helpful!