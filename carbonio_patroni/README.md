# Ansible Collection - zxbot.carbonio_patroni

An ansible collection to install Patroni part of Carbonio Cluster Services Redundancy 

To install Kafka using this collection you have to insert new groups in the inventory file It supports only FQDN.

## Table of Contents

- [Prerequisites](#prerequisites)
- [Install the Collection](#install-the-collection)
- [Modify the Inventory](#modify-the-inventory)
- [Full Cluster Services Redundancy Inventory Example](#full-cluster-services-redundancy-inventory-example)
- [Important Notes on Initial Roles](#important-notes-on-initial-roles-for-cluster-services-redundancy-configuration)
- [Confirmation](#confirmation)
- [Install PostgreSQL Redundancy](#install-postgresql-redundancy)
- [License(s)](#licenses)

## Prerequisites

- The inventory must use FQDN only — this collection supports FQDN entries exclusively.
- New groups must be added to the inventory file: `postgresServers` (with `postgres_version` and `patroni_role` variables) and `dbsConnectorServers` (see [Modify the Inventory](#modify-the-inventory)).

### Install the collection

```
ansible-galaxy collection install zxbot.carbonio_patroni
```

### Modify the inventory 

To configure the inventory for Cluster Services Redundancy installation, update the **inventory file** with specific variables and add the following groups:

`postgresServers` group includes the following variables:
* `postgres_version` Specifies the PostgreSQL version used for PostgreSQL redundancy.
* `patroni_role` Specifies the Patroni role. Use primary for the initial master or secondary for additional masters.
```
[postgresServers]
svcs1.example.com postgres_version=16 patroni_role=primary
svcs2.example.com postgres_version=16 patroni_role=secondary
```

`dbsConnectorServers` group specifies db connectors due to Cluster Services Redundancy  (it will move connectors from postgres to application servers)
```
[dbsConnectorServers]
mbox1.example.com 
mbox2.example.com
```

## Full Cluster Services Redundancy Inventory Example

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

#Custom Default Domain (Optional)
[masterDirectoryServers:vars]
# Replace domain.com with your desired domain
#default_domain=domain.com

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
webmailHostname=webmailPublicHostname

[applicationServers]
mbox1.example.com
mbox2.example.com

[filesServers]
filesdocs1.example.com
filesdocs2.example.com

[taskServers]
filesdocs1.example.com
filesdocs2.example.com

[docsServers]
filesdocs1.example.com
filesdocs2.example.com

[previewServers]
filesdocs1.example.com
filesdocs2.example.com

[videoServers]
#hostname public_ip_address=x.y.z.t
video1.example.com
video2.example.com

[workStreamServers]
chats1.example.com
chats2.example.com

[prometheusServers]
svcs3.example.com

[syslogServer]
svcs3.example.com
```

### Important Notes on Initial Roles for Cluster Services Redundancy configuration

The initial roles assigned during the standard installation must remain on the servers configured in the standard environment. Follow these guidelines:

```plaintext
- Do not assign  the `primary` role to any additional servers being configured as extra masters.
  
- Additional servers should be assigned:
  - `secondary` for PostgreSQL
```

## Confirmation

The PostgreSQL redundancy setup contains different confirmation checks depending on the playbook being executed.

### PostgreSQL Replica Installation

Before configuring additional PostgreSQL replicas, the playbook displays:

- the currently installed Carbonio version detected on the existing PostgreSQL server;
- the Patroni collection source;
- the Patroni collection version.

Verify that the Patroni collection version is appropriate for the currently installed Carbonio infrastructure before continuing.

The PostgreSQL replica installation does not validate the Zextras repository because this stage does not install Carbonio DB connector packages.

### Patroni HA Setup

Before configuring Patroni and moving the DB connectors, the playbook displays:

- the currently installed Carbonio version;
- the Patroni collection source;
- the Patroni collection version;
- the configured Zextras repository.

The repository is validated on the current DB connector server and the target `dbsConnectorServers`.

Before continuing, verify that:

- the existing Carbonio infrastructure has been updated to the intended version;
- the Patroni collection version is appropriate for the installed Carbonio version;
- the configured Zextras repository matches the currently installed Carbonio version;
- Carbonio packages installed during the HA setup will match the versions used by the existing infrastructure.

If the repository is missing, multiple repositories are configured on a server, or different repositories are detected between the current and target DB connector servers, the playbook stops before applying the HA configuration.

To skip the interactive confirmation, set:

```
carbonio_auto_confirm_repository_and_playbook: true
```

When automatic confirmation is enabled, the detected environment information is still displayed and repository validation is still performed where applicable.

###  Install PostgreSQL redundancy

Run these commands to set up PostgreSQL redundancy with Patroni:
```
ansible-playbook -i inventory -u root zxbot.carbonio_patroni.carbonio_replica_postgres_install
ansible-playbook -i inventory -u root zxbot.carbonio_patroni.carbonio_patroni_install
```

## License(s)

See [COPYING](COPYING.md) file for detail.