# Ansible Collection - zxbot.carbonio_ldap

An ansible collection to install Multi Master LDAP part of Carbonio Cluster Services Redundancy 

To install Multi Master LDAP using this collection you have to modify the masterDirectoryServers group in the inventory file. It supports only FQDN.

## Table of Contents

- [Prerequisites](#prerequisites)
- [Install the Collection](#install-the-collection)
- [Modify the Inventory](#modify-the-inventory)
- [Full Cluster Services Redundancy Inventory Example](#full-cluster-services-redundancy-inventory-example)
- [Important Notes on Initial Roles](#important-notes-on-initial-roles-for-cluster-services-redundancy-configuration)
- [Confirmation](#confirmation)
- [Install Multi-Master LDAP](#install-multi-master-ldap)
- [License(s)](#licenses)

## Prerequisites

- The inventory must use FQDN only — this collection supports FQDN entries exclusively.
- The `masterDirectoryServers` group in the inventory must be modified to include an `ldap_role` for each host (see [Modify the Inventory](#modify-the-inventory)).

### Install the collection

```
ansible-galaxy collection install zxbot.carbonio_ldap
```

### Modify the inventory 

To configure the inventory for Cluster Services Redundancy installation, update the **inventory file** with specific variables and add the following groups:

`masterDirectoryServers` group includes the following variable:
* `ldap_role` Specifies the LDAP role. Use master for the initial master or mmr for additional masters.
```
[masterDirectoryServers]
svcs1.example.com ldap_role=master
svcs2.example.com ldap_role=mmr
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
- Do not assign the `master` role to any additional servers being configured as extra masters.
  
- Additional servers should be assigned:
  - `mmr` for LDAP
```

## Confirmation

Before configuring LDAP Multi-Master, the playbook displays:

- the currently installed Carbonio version;
- the LDAP HA collection source;
- the LDAP HA collection version;
- the configured Zextras repository.

The playbook verifies that the same Zextras repository is configured on all LDAP servers.

Before continuing, verify that:

- the existing Carbonio infrastructure has been updated to the intended version;
- the LDAP HA collection version is appropriate for the installed Carbonio version;
- the configured Zextras repository matches the currently installed Carbonio version;
- Carbonio packages installed during the LDAP HA setup will match the versions used by the existing infrastructure.

If a Zextras repository is missing, multiple repositories are configured on a server, or different repositories are detected across the LDAP servers, the playbook stops before applying the HA configuration.

To skip the interactive confirmation, set:

```
carbonio_auto_confirm_repository_and_playbook: true
```

When automatic confirmation is enabled, the detected environment information is still displayed and repository validation is still performed.

### Install Multi-Master LDAP

Run this command to install LDAP in a multi-master configuration:
```
ansible-playbook -i inventory -u root zxbot.carbonio_ldap.carbonio_install_mmr
```

## License(s)

See [COPYING](COPYING.md) file for detail.