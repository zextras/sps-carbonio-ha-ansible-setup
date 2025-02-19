# Ansible Collection - zxbot.carbonio_kafka

An ansible collection to install Kafka&Zookeeper part of Carbonio HA 

To install Kafka and Zookeeper using this collection you have to insert new groups in the inventory file It supports only FQDN.

### Install the collection

```
ansible-galaxy collection install zxbot.carbonio_kafka
```
### Modify the inventory 

To configure the inventory for HA installation, update the **inventory file** with specific variables and add the following groups:

`kafka` group specifies the servers where Kafka will be installed:
```
[kafka]
svc1.example.com broker_id=1
svc2.example.com broker_id=2
svc3.example.com broker_id=3
```

`zookeeper_servers` group specifies the servers where Zookeeper will be installed:
```
[zookeeper_servers]
svc1.example.com zookeeper_id=1
svc2.example.com zookeeper_id=2
svc3.example.com zookeeper_id=3
```

Example for Full HA inventory file

```
[kafka]
svcs1.example.com broker_id=1
svcs2.example.com broker_id=2
svcs3.example.com broker_id=3

[zookeeper_servers]
svcs1.example.com zookeeper_id=1
svcs2.example.com zookeeper_id=2
svcs3.example.com zookeeper_id=3

[postgresServers]
svcs1.example.com postgres_version=16 patroni_role=primary
svcs2.example.com postgres_version=16 patroni_role=secondary

[masterDirectoryServers]
svcs1.example.com ldap_role=master
svcs2.example.com ldap_role=mmr

[replicaDirectoryServers]

 
[serviceDiscoverServers]
svcs1.example.com
svcs2.example.com
svcs3.example.com

[dbsConnectorServers]
mbox1.example.com
mbox2.example.com

[mtaServers]
mta1.example.com
mta2.example.com

[proxyServers]
proxy1.example.com
proxy2.example.com

[proxyServers:vars]
webmailHostname=webmail.demo.zextras.io

[applicationServers]
mbox1.example.com
mbox2.example.com

[filesServers]
filesdocs2.example.com
filesdocs1.example.com

[taskServers]
filesdocs2.example.com
filesdocs1.example.com

[docsServers]
filesdocs2.example.com
filesdocs1.example.com

[previewServers]
filesdocs2.example.com
filesdocs1.example.com

[videoServers]
#hostname public_ip_address=x.y.z.t
video1.example.com
video2.example.com

[prometheusServers]
svcs3.example.com

[syslogServer]
svcs3.example.com
```

### Install Zookeeper and Kafka
Run the following commands to install Zookeeper and Kafka:
```
ansible-playbook -i inventory zxbot.carbonio_kafka.carbonio_zookeper_install
ansible-playbook -i inventory zxbot.carbonio_kafka.carbonio_kafka_install
```

## License(s)

See [COPYING](COPYING.md) file for detail.
