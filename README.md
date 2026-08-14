# Carbonio Cluster Services Redundancy

This repository contains Ansible collections used to configure redundancy for core Carbonio cluster services, including Kafka, PostgreSQL with Patroni, and LDAP Multi-Master.

These components are used to prepare an existing Carbonio infrastructure for Cluster Services Redundancy and Mail Replica.

## Table of Contents

- [Collections Overview](#collections-overview)
- [Prerequisites](#prerequisites)
- [Install the Collections](#install-the-collections)
- [Update the Inventory](#update-the-inventory)
- [Important Notes on Initial Roles](#important-notes-on-initial-roles)
- [Full Inventory Example](#full-inventory-example)
- [Confirmation](#confirmation)
- [Installation Steps](#installation-steps)
- [License(s)](#licenses)

## Collections Overview

### Kafka

The `carbonio_kafka` collection installs and configures Kafka for Carbonio Cluster Services Redundancy.

### PostgreSQL and Patroni

The `carbonio_patroni` collection provides two playbooks:

1. installation and configuration of additional PostgreSQL replicas;
2. Patroni and HAProxy configuration and migration of Carbonio DB connectors.

### LDAP Multi-Master

The `carbonio_ldap` collection configures an additional Carbonio Directory Server as an LDAP Multi-Master node.

## Prerequisites

The standard Carbonio installation must be completed before configuring Cluster Services Redundancy.

The existing Carbonio infrastructure must also be updated to the intended version before starting the HA setup.

The inventory must use FQDN entries.

After the standard Carbonio installation, keep the following inventory files in the same directory:

```
inventory
inventory_postgrespassword
inventory_ldap_password
inventory_consulpassword
```

The prerequisite checks validate the required inventory and password files before the HA configuration starts.

## Install the Collections

Install the required collections from Ansible Galaxy:

```
ansible-galaxy collection install zxbot.carbonio_kafka
ansible-galaxy collection install zxbot.carbonio_patroni
ansible-galaxy collection install zxbot.carbonio_ldap
```

## Update the Inventory

### Kafka

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

### PostgreSQL

The `postgresServers` group uses:

- `postgres_version` to specify the PostgreSQL version;
- `patroni_role=primary` for the existing PostgreSQL server;
- `patroni_role=secondary` for additional PostgreSQL servers.

The existing PostgreSQL server must remain the first server in the group:

```
[postgresServers]
svc1.example.com postgres_version=16 patroni_role=primary
svc2.example.com postgres_version=16 patroni_role=secondary
```

### LDAP

The `masterDirectoryServers` group uses:

- `ldap_role=master` for the existing Directory Server;
- `ldap_role=mmr` for additional Multi-Master Directory Servers.

The existing Directory Server must remain the first server in the group:

```
[masterDirectoryServers]
svc1.example.com ldap_role=master
svc2.example.com ldap_role=mmr
```

A custom default domain can be configured when required:

```
[masterDirectoryServers:vars]
# default_domain=domain.com
```

### DB Connectors

The `dbsConnectorServers` group defines the target servers where Carbonio DB connectors will be moved during the Patroni HA setup:

```
[dbsConnectorServers]
mbox1.example.com
mbox2.example.com
```

## Important Notes on Initial Roles

The roles assigned during the standard Carbonio installation must remain assigned to the original servers.

Do not assign the initial roles to additional HA servers:

```
LDAP:
  existing server    -> ldap_role=master
  additional server  -> ldap_role=mmr

PostgreSQL:
  existing server    -> patroni_role=primary
  additional server  -> patroni_role=secondary
```

The existing LDAP master and PostgreSQL primary must remain the first entries in their respective inventory groups.

## Full Inventory Example

```
[kafka]
svc1.example.com broker_id=1
svc2.example.com broker_id=2
svc3.example.com broker_id=3

[zookeeper_servers]

[postgresServers]
# The PostgreSQL server from the standard Carbonio installation
# must remain the first server in this group.
svc1.example.com postgres_version=16 patroni_role=primary
svc2.example.com postgres_version=16 patroni_role=secondary

[masterDirectoryServers]
# The Directory Server from the standard Carbonio installation
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

The Cluster Services Redundancy playbooks display the relevant environment and collection information before applying HA configuration changes.

Depending on the collection and setup stage, the confirmation includes:

- the currently installed Carbonio version;
- the Ansible collection source;
- the Ansible collection version;
- the configured Zextras repository when Carbonio packages will be installed.

Before continuing, verify that the existing Carbonio infrastructure has been updated to the intended version and that the collection version and repository configuration are appropriate for the installed Carbonio version.

### Kafka

The Kafka playbook displays the Kafka collection source and version.

The Zextras repository is not validated because the Kafka playbook does not install Carbonio packages.

Interactive confirmation can be skipped with:

```
carbonio_auto_confirm_playbook: true
```

### PostgreSQL and Patroni

The PostgreSQL replica installation displays the installed Carbonio version and Patroni collection information.

The Patroni HA setup additionally validates the Zextras repository used by the current and target DB connector servers.

Interactive confirmation can be skipped with:

```
carbonio_auto_confirm_repository_and_playbook: true
```

### LDAP Multi-Master

The LDAP Multi-Master setup displays the installed Carbonio version and LDAP collection information and validates the Zextras repository configured on the LDAP servers.

Interactive confirmation can be skipped with:

```
carbonio_auto_confirm_repository_and_playbook: true
```

When automatic confirmation is enabled, the detected environment information is still displayed before the setup continues.

## Installation Steps

For a complete Cluster Services Redundancy setup, execute the playbooks in the required sequence.

### 1. Install Kafka

```
ansible-playbook -i inventory -u root zxbot.carbonio_kafka.carbonio_kafka_install
```

### 2. Install PostgreSQL Replicas

```
ansible-playbook -i inventory -u root zxbot.carbonio_patroni.carbonio_replica_postgres_install
```

### 3. Configure Patroni

After the PostgreSQL replica installation completes successfully:

```
ansible-playbook -i inventory -u root zxbot.carbonio_patroni.carbonio_patroni_install
```

### 4. Configure LDAP Multi-Master

```
ansible-playbook -i inventory -u root zxbot.carbonio_ldap.carbonio_install_mmr
```

The individual collections can also be used separately when only a specific Cluster Services Redundancy component needs to be configured.

## License(s)

See [COPYING](COPYING.md) for details.