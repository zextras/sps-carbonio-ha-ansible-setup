# Ansible Collection - zxbot.carbonio_kafka

An Ansible collection to install and configure Kafka as part of Carbonio Cluster Services Redundancy.

The collection installs Kafka on the servers defined in the `kafka` inventory group and configures the Kafka brokers used by Carbonio Application Servers.

## Table of Contents

- [Prerequisites](#prerequisites)
- [Install the Collection](#install-the-collection)
- [Modify the Inventory](#modify-the-inventory)
- [Full Cluster Services Redundancy Inventory Example](#full-cluster-services-redundancy-inventory-example)
- [Confirmation](#confirmation)
- [Install Kafka](#install-kafka)
- [License(s)](#licenses)

## Prerequisites

- The inventory must use FQDN entries.
- The existing Carbonio infrastructure must be updated to the intended version before starting the HA setup.
- A `kafka` group must be added to the inventory.
- Each Kafka server must have a unique `broker_id`.

## Install the Collection

Install the collection from Ansible Galaxy:

```
ansible-galaxy collection install zxbot.carbonio_kafka
```

## Modify the Inventory

Add the `kafka` group and assign a unique `broker_id` to each Kafka server:

```
[kafka]
svc1.example.com broker_id=1
svc2.example.com broker_id=2
svc3.example.com broker_id=3
```

The `zookeeper_servers` group is deprecated for new installations starting from Carbonio 25.9.0 because ZooKeeper has been replaced by Kafka KRaft. Keep the group empty:

```
[zookeeper_servers]
```

## Full Cluster Services Redundancy Inventory Example

```
[kafka]
svc1.example.com broker_id=1
svc2.example.com broker_id=2
svc3.example.com broker_id=3

[zookeeper_servers]

[postgresServers]
svc1.example.com postgres_version=16 patroni_role=primary
svc2.example.com postgres_version=16 patroni_role=secondary

[masterDirectoryServers]
# The master installed during the standard Carbonio installation
# must remain the first server in this group.
svc1.example.com ldap_role=master
svc2.example.com ldap_role=mmr

[masterDirectoryServers:vars]
# Custom Default Domain (Optional)
# default_domain=domain.com

[replicaDirectoryServers]

[serviceDiscoverServers]
svc1.example.com
svc2.example.com
svc3.example.com

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
# webmailHostname=webmail.example.com

[applicationServers]
mbox1.example.com
mbox2.example.com

[filesServers]
filesdocs1.example.com
filesdocs2.example.com

[docsServers]
filesdocs1.example.com
filesdocs2.example.com

[taskServers]
filesdocs1.example.com
filesdocs2.example.com

[previewServers]
filesdocs1.example.com
filesdocs2.example.com

[videoServers]
# hostname public_ip_address=x.y.z.t
video1.example.com public_ip_address=1.2.3.4
video2.example.com public_ip_address=1.2.3.5

[workStreamServers]
chats1.example.com
chats2.example.com

[prometheusServers]
svc3.example.com

[syslogServer]
svc3.example.com
```

## Confirmation

Before installing Kafka, the playbook displays:

- the Kafka collection source;
- the Kafka collection version.

Verify that the collection version is appropriate for the currently installed Carbonio infrastructure before continuing.

The Kafka installation does not require validation of the Zextras repository because Carbonio packages are not installed by this playbook.

To skip the interactive confirmation, set:

```
carbonio_auto_confirm_playbook: true
```

When automatic confirmation is enabled, the playbook information is still displayed before the installation continues.

## Install Kafka

Run the following command:

```
ansible-playbook -i inventory -u root zxbot.carbonio_kafka.carbonio_kafka_install
```

## License(s)

See [COPYING](COPYING.md) for details.