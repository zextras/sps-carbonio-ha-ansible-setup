# Carbonio Cluster Services Redundancy

This repository contains separate Ansible playbooks that enables redundacy for Carbonio services like Directory, Postgres and Kafka that are mandatory to implement Mail Replica.

## Table of Contents

- [Playbooks Overview](#playbooks-overview)
- [Usage Notes](#usage-notes)
- [Prerequisites](#prerequisites)
- [Update the Inventory](#update-the-inventory)
- [Important Notes on Initial Roles](#important-notes-on-initial-roles-for-cluster-services-redundancy-configuration)
- [Full Inventory Example](#full-inventory-example)
- [Confirmation](#confirmation)
- [Installation Steps](#installation-steps)
- [License(s)](#licenses)

This repository contains separate Ansible playbooks that enables redundacy for Carbonio services like Directory, Postgres and Kafka that are mandatory to implement Mail Replica.
## Playbooks Overview

### 1. **Kafka**
- `carbonio_kafka` includes a playbook to install Kafka, essential for distributed messaging and coordination.

### 2. **LDAP Multi-Master**
- `carbonio_ldap` includes a playbook to install LDAP multi-master.

### 3. **PostgreSQL and Patroni**
- `carbonio_patroni` includes 2 playbooks to set up a PostgreSQL replica cluster and install Patroni for PostgreSQL management (second one includes HAProxy).

## Usage Notes

- These playbooks can be executed individually to set up specific Cluster Services Redundancy components.
- For a **complete Cluster Services Redundancy setup**, ensure all playbooks are executed in the correct sequence.

## Prerequisites

### Inventory Files
After the standard Carbonio installation, the following inventory files should be present:
- `inventory`
- `inventory_postgrespassword`
- `inventory_ldap_password`
- `inventory_consulpassword`

The playbook validates that these password files exist and are located in the same folder as the inventory file — the run will fail early if they're missing or misplaced.

### Update the Inventory
To configure the inventory for Cluster Services Redundancy installation, update the **inventory file** with specific variables and add the following groups:

`kafka` group specifies the servers where Kafka will be installed:
```
[kafka]
svcs1.example.com broker_id=1
svcs2.example.com broker_id=2
svcs3.example.com broker_id=3
```

`zookeeper_servers` group is deprecated for new installations, keep it empty as Zookeeper has been replaced by Kafka Kraft and will no longer be used
```
[zookeeper_servers]
#Starting from 25.9.0 this group is deprecated for new installations, keep it empty as Zookeeper has been replaced by Kafka Kraft and will no longer be used
```

`postgresServers` group includes the following variables:
* `postgres_version` Specifies the PostgreSQL version used for PostgreSQL redundancy.
* `patroni_role` Specifies the Patroni role. Use primary for the initial master or secondary for additional masters.
```
[postgresServers]
svcs1.example.com postgres_version=16 patroni_role=primary
svcs2.example.com postgres_version=16 patroni_role=secondary
```

`masterDirectoryServers` group includes the following variable:
* `ldap_role` Specifies the LDAP role. Use master for the initial master or mmr for additional masters.
```
[masterDirectoryServers]
svcs1.example.com ldap_role=master
svcs2.example.com ldap_role=mmr
```

`dbsConnectorServers` group specifies db connectors due to Cluster Services Redundancy (it will move connectors from postgres to application servers)
```
[dbsConnectorServers]
mbox1.example.com 
mbox2.example.com
```

### Important Notes on Initial Roles for Cluster Services Redundancy configuration

The initial roles assigned during the standard installation must remain on the servers configured in the standard environment. Follow these guidelines:

```plaintext
- Do not assign the `master` role (for LDAP) or the `primary` role (for PostgreSQL) 
  to any additional servers being configured as extra masters.
  
- Additional servers should be assigned:
  - `mmr` for LDAP
  - `secondary` for PostgreSQL
```

### Full Inventory Example
Here’s an example of the inventory file configured for Cluster Services Redundancy:
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
#webmailHostname=webmailPublicHostname

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
#hostname public_ip_address=x.y.z.t
video1.example.com public_ip_address=1.2.3.4
video2.example.com public_ip_address=1.2.3.4

[workStreamServers]
chats1.example.com
chats2.example.com

[prometheusServers]
svcs3.example.com

[syslogServer]
svcs3.example.com
```

## Confirmation

Cluster Services Redundancy playbooks display the relevant environment and collection information before applying HA configuration changes.

Depending on the playbook, the confirmation includes:

- the currently installed Carbonio version;
- the Ansible collection source and version;
- the configured Zextras repository when Carbonio packages are installed.

### Kafka

The Kafka installation displays the Kafka collection source and version.

The Zextras repository is not validated because the Kafka playbook does not install Carbonio packages.

Interactive confirmation can be skipped with:

```
carbonio_auto_confirm_playbook: true
```

### PostgreSQL and Patroni

The PostgreSQL replica installation displays the currently installed Carbonio version and Patroni collection source/version.

Repository validation is not required during the PostgreSQL replica installation.

The Patroni HA setup additionally displays and validates the Zextras repository on the current and target DB connector servers.

Interactive confirmation can be skipped with:

```
carbonio_auto_confirm_repository_and_playbook: true
```

### LDAP Multi-Master

The LDAP Multi-Master setup displays the currently installed Carbonio version, LDAP collection source/version, and configured Zextras repository.

The same repository must be configured on all LDAP servers before the HA setup can continue.

Interactive confirmation can be skipped with:

```
carbonio_auto_confirm_repository_and_playbook: true
```

Automatic confirmation skips only the interactive prompt. The detected environment information is still displayed and repository validation is still performed where applicable.

## Installation Steps

Download necessary collections before the Cluster Services Redundancy installation:

```
ansible-galaxy collection install zxbot.carbonio_kafka
ansible-galaxy collection install zxbot.carbonio_patroni
ansible-galaxy collection install zxbot.carbonio_ldap
```

### 1. Install Kafka
Run the following command to install Kafka:
```
ansible-playbook -i inventory -u root zxbot.carbonio_kafka.carbonio_kafka_install
```

### 2. Install PostgreSQL redundancy
Run these commands to set up PostgreSQL redundancy with Patroni:
```
ansible-playbook -i inventory -u root zxbot.carbonio_patroni.carbonio_replica_postgres_install
ansible-playbook -i inventory -u root zxbot.carbonio_patroni.carbonio_patroni_install
```

### 3. Install Multi-Master LDAP
Run this command to install LDAP in a multi-master configuration:
```
ansible-playbook -i inventory -u root zxbot.carbonio_ldap.carbonio_install_mmr
```

## License(s)

See [COPYING](COPYING.md) file for detail.