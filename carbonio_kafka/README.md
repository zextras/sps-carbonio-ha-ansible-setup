# Ansible Collection - zxbot.carbonio_kafka

An ansible collection to install Kafka part of Carbonio Cluster Services Redundancy 

To install Kafka using this collection you have to insert new group in the inventory file It supports only FQDN.

### Install the collection

```
ansible-galaxy collection install zxbot.carbonio_kafka
```
### Modify the inventory 

To configure the inventory for Cluster Services Redundancy installation, update the **inventory file** with specific variables and add the following groups:

`kafka` group specifies the servers where Kafka will be installed:
```
[kafka]
svc1.example.com broker_id=1
svc2.example.com broker_id=2
svc3.example.com broker_id=3
```

Example for Full Cluster Services Redundancy inventory file

```
[kafka]
svcs1.example.com broker_id=1
svcs2.example.com broker_id=2
svcs3.example.com broker_id=3

[zookeeper_servers]
#Starting from 25.9.0 this group is deprecated for new installations, keep it empty as Zookeeper has been replaced by Kafka Kraft and will no longer be used

[postgresServers]
svcs1.example.com postgres_version=16 patroni_role=primary
svcs2.example.com postgres_version=16 patroni_role=secondary

[masterDirectoryServers]
#The master installed in the previous step should be first in the list
svcs1.example.com ldap_role=master
svcs2.example.com ldap_role=mmr

[replicaDirectoryServers]
#Keep this group empty for User Mail Replica installation
 
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

[workStreamServers]
wsc1.example.com
wsc2.example.com

[prometheusServers]
svcs3.example.com

[syslogServer]
svcs3.example.com
```

### Install Kafka
Run the following command to install Kafka:
```
ansible-playbook -i inventory zxbot.carbonio_kafka.carbonio_kafka_install
```

## License(s)

See [COPYING](COPYING.md) file for detail.
