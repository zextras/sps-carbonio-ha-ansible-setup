# Ansible Collection - zxbot.carbonio_patroni

An Ansible collection to configure PostgreSQL redundancy and Patroni as part of Carbonio Cluster Services Redundancy.

The setup is performed in two steps:

1. Install and configure additional PostgreSQL replicas.
2. Configure Patroni, HAProxy, and move Carbonio DB connectors to the servers defined in `dbsConnectorServers`.

## Table of Contents

- [Prerequisites](#prerequisites)
- [Install the Collection](#install-the-collection)
- [Modify the Inventory](#modify-the-inventory)
- [Important Notes on Initial Roles](#important-notes-on-initial-roles)
- [Full Cluster Services Redundancy Inventory Example](#full-cluster-services-redundancy-inventory-example)
- [Confirmation](#confirmation)
- [Install PostgreSQL Redundancy](#install-postgresql-redundancy)
- [License(s)](#licenses)

## Prerequisites

- The inventory must use FQDN entries.
- The existing Carbonio infrastructure must be updated to the intended version before starting the HA setup.
- The `postgresServers` group must contain the existing PostgreSQL server and the additional PostgreSQL servers.
- Each PostgreSQL server must have the appropriate `postgres_version` and `patroni_role`.
- The existing PostgreSQL server must remain the first server in `postgresServers`.
- The `dbsConnectorServers` group must contain the target servers where Carbonio DB connectors will be moved.
- The Zextras repository configured on the current and target DB connector servers must provide packages compatible with the currently installed Carbonio version.

## Install the Collection

Install the collection from Ansible Galaxy:

```
ansible-galaxy collection install zxbot.carbonio_patroni
```

## Modify the Inventory

The `postgresServers` group uses the following variables:

- `postgres_version` specifies the PostgreSQL version.
- `patroni_role` specifies the role of the PostgreSQL server:
  - `primary` for the PostgreSQL server created during the standard Carbonio installation;
  - `secondary` for additional PostgreSQL servers.

The existing PostgreSQL server must remain the first server in the group:

```
[postgresServers]
svc1.example.com postgres_version=16 patroni_role=primary
svc2.example.com postgres_version=16 patroni_role=secondary
```

The `dbsConnectorServers` group defines the target servers where Carbonio DB connectors will be moved during the Patroni HA setup:

```
[dbsConnectorServers]
mbox1.example.com
mbox2.example.com
```

## Important Notes on Initial Roles

The role assigned to the existing PostgreSQL server during the standard Carbonio installation must not be changed.

Do not assign `patroni_role=primary` to additional PostgreSQL servers.

Additional PostgreSQL servers must use:

```
patroni_role=secondary
```

The existing PostgreSQL server must remain the first entry in `postgresServers`.

## Full Cluster Services Redundancy Inventory Example

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

The PostgreSQL redundancy setup contains two confirmation stages.

### PostgreSQL Replica Installation

Before configuring additional PostgreSQL replicas, the playbook displays:

- the currently installed Carbonio version detected on the existing PostgreSQL server;
- the Patroni collection source;
- the Patroni collection version.

Verify that the collection version is appropriate for the currently installed Carbonio infrastructure before continuing.

The PostgreSQL replica installation does not validate the Zextras repository because this stage installs PostgreSQL packages rather than Carbonio DB connector packages.

### Patroni HA Setup

Before configuring Patroni and moving the DB connectors, the playbook detects and displays:

- the currently installed Carbonio version;
- the Patroni collection source;
- the Patroni collection version;
- the configured Zextras repository.

The repository is validated on the current DB connector server and the target DB connector servers to ensure that Carbonio packages installed during the migration are obtained from the same repository.

Before continuing, verify that:

- the existing Carbonio infrastructure has been updated to the intended version;
- the Patroni collection version is appropriate for the installed Carbonio version;
- the configured Zextras repository matches the currently installed Carbonio version;
- Carbonio packages installed during the HA setup will match the versions used by the existing infrastructure.

To skip the interactive confirmation, set:

```
carbonio_auto_confirm_repository_and_playbook: true
```

When automatic confirmation is enabled, the detected environment information is still displayed before the setup continues.

## Install PostgreSQL Redundancy

PostgreSQL redundancy must be configured in two steps.

### 1. Install PostgreSQL Replicas

Run:

```
ansible-playbook -i inventory -u root zxbot.carbonio_patroni.carbonio_replica_postgres_install
```

Wait for the playbook to complete successfully before continuing.

### 2. Configure Patroni

Run:

```
ansible-playbook -i inventory -u root zxbot.carbonio_patroni.carbonio_patroni_install
```

This playbook configures Patroni and HAProxy and moves the Carbonio DB connectors to the servers defined in `dbsConnectorServers`.

The two playbooks must be executed in this order.

## License(s)

See [COPYING](COPYING.md) for details.