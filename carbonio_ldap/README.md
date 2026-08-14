# Ansible Collection - zxbot.carbonio_ldap

An Ansible collection to configure LDAP Multi-Master as part of Carbonio Cluster Services Redundancy.

The collection extends an existing Carbonio Directory Server installation by configuring an additional Directory Server as a Multi-Master LDAP node.

## Table of Contents

- [Prerequisites](#prerequisites)
- [Install the Collection](#install-the-collection)
- [Modify the Inventory](#modify-the-inventory)
- [Important Notes on Initial Roles](#important-notes-on-initial-roles)
- [Full Cluster Services Redundancy Inventory Example](#full-cluster-services-redundancy-inventory-example)
- [Confirmation](#confirmation)
- [Install Multi-Master LDAP](#install-multi-master-ldap)
- [License(s)](#licenses)

## Prerequisites

- The inventory must use FQDN entries.
- The existing Carbonio infrastructure must be updated to the intended version before starting the HA setup.
- The `masterDirectoryServers` group must contain the existing Directory Server and the additional server that will be configured for Multi-Master LDAP.
- Each server in `masterDirectoryServers` must have the appropriate `ldap_role`.
- The Zextras repository configured on the LDAP servers must provide packages compatible with the currently installed Carbonio version.

## Install the Collection

Install the collection from Ansible Galaxy:

```
ansible-galaxy collection install zxbot.carbonio_ldap
```

## Modify the Inventory

The `masterDirectoryServers` group uses the `ldap_role` variable:

- `master` identifies the Directory Server created during the standard Carbonio installation.
- `mmr` identifies an additional Directory Server that will be configured for Multi-Master LDAP.

The existing master must remain the first server in the group:

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

## Important Notes on Initial Roles

The role assigned to the existing Directory Server during the standard Carbonio installation must not be changed.

Do not assign `ldap_role=master` to additional Directory Servers.

Additional Directory Servers must use:

```
ldap_role=mmr
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

Before configuring LDAP Multi-Master, the playbook detects and displays:

- the currently installed Carbonio version;
- the LDAP HA collection source;
- the LDAP HA collection version;
- the configured Zextras repository.

The playbook also verifies that the same Zextras repository is configured on the LDAP servers.

Before continuing, verify that:

- the existing Carbonio infrastructure has been updated to the intended version;
- the LDAP HA collection version is appropriate for the installed Carbonio version;
- the configured Zextras repository matches the currently installed Carbonio version;
- Carbonio packages installed during the LDAP HA setup will match the versions used by the existing infrastructure.

To skip the interactive confirmation, set:

```
carbonio_auto_confirm_repository_and_playbook: true
```

When automatic confirmation is enabled, the detected environment information is still displayed before the setup continues.

## Install Multi-Master LDAP

Run the following command:

```
ansible-playbook -i inventory -u root zxbot.carbonio_ldap.carbonio_install_mmr
```

## License(s)

See [COPYING](COPYING.md) for details.