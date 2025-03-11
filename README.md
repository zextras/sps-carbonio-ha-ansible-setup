# Carbonio High Availability (HA) Installation

This repository contains separate Ansible playbooks designed to configure Carbonio HA. 

## Playbooks Overview

### 1. **Kafka and Zookeeper**
- `carbonio_kafka` includes 2 playboks to install Kafka and Zookeeper, essential for distributed messaging and coordination.

### 2. **LDAP Multi-Master**
- `carbonio_ldap` includes 2 playboks to install (or promote if replica is installed) LDAP multi-master.

### 3. **PostgreSQL and Patroni**
- `carbonio_patroni` includes 2 playbooks to sets up a PostgreSQL replica cluster and install Patroni for PostgreSQL HA management (second one includes HAProxy).

## Usage Notes

- These playbooks can be executed individually to set up specific HA components.
- For a **complete HA setup**, ensure all playbooks are executed in the correct sequence.

## Prerequisites

### Inventory Files
After the standard Carbonio installation, the following inventory files should be present:
- `inventory`
- `inventory_postgrespassword`
- `inventory_ldap_password`
- `inventory_consulpassword`
- `inventory_videoserver` (only if the video server was installed)

### Update the Inventory
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

`postgresServers` group includes the following variables:
* `postgres_version` Specifies the PostgreSQL version used for PostgreSQL HA.
* `patroni_role` Specifies the Patroni role. Use primary for the initial master or secondary for additional masters.
```
[postgresServers]
svc1.example.com postgres_version=16 patroni_role=primary
svc2.example.com postgres_version=16 patroni_role=secondary
```

`masterDirectoryServers` group includes the following variable:
* `ldap_role` Specifies the LDAP role. Use master for the initial master or mmr for additional masters.
```
[masterDirectoryServers]
svc1.example.com ldap_role=master
svc2.example.com ldap_role=mmr
```

`dbsConnectorServers` group specifies db connectors for HA (it will move connectors from postgres to application servers)
```
[dbsConnectorServers]
mbox1.example.com 
mbox2.example.com
```

### Important Notes on Initial Roles for HA Configuration

The initial roles assigned during the standard installation must remain on the servers configured in the standard environment. Follow these guidelines:

```plaintext
- Do not assign the `master` role (for LDAP) or the `primary` role (for PostgreSQL) 
  to any additional servers being configured as extra masters.
  
- Additional servers should be assigned:
  - `mmr` for LDAP
  - `secondary` for PostgreSQL
```
### Full Inventory Example
Here’s an example of the inventory file configured for HA:
```
[kafka]
svc1.example.com broker_id=1
svc2.example.com broker_id=2
svc3.example.com broker_id=3

[zookeeper_servers]
svc1.example.com zookeeper_id=1
svc2.example.com zookeeper_id=2
svc3.example.com zookeeper_id=3

[postgresServers]
svc1.example.com postgres_version=16 patroni_role=primary
svc2.example.com postgres_version=16 patroni_role=secondary

[masterDirectoryServers]
svc1.example.com ldap_role=master
svc2.example.com ldap_role=mmr

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
wsc1.example.com

[prometheusServers]
svc3.example.com

[syslogServer]
svc3.example.com
```

## Installation Steps

Download necessary collections before the HA installation:

```
ansible-galaxy collection install zxbot.carbonio_kafka
ansible-galaxy collection install zxbot.carbonio_patroni
ansible-galaxy collection install zxbot.carbonio_ldap
```


### 1. Install Zookeeper and Kafka
Run the following commands to install Zookeeper and Kafka:
```
ansible-playbook -i inventory zxbot.carbonio_kafka.carbonio_zookeper_install
ansible-playbook -i inventory zxbot.carbonio_kafka.carbonio_kafka_install
```

### 2. Install PostgreSQL HA
Run these commands to set up PostgreSQL HA with Patroni:
```
ansible-playbook -i inventory zxbot.carbonio_patroni.carbonio_replica_postgres_install
ansible-playbook -i inventory zxbot.carbonio_patroni.carbonio_patroni_install
```
**Note:** During the execution of the Patroni playbook, you will be prompted with the following question:
 
```
Is this a full HA installation? (yes/no)
```
 
- If you answer `yes`, HAProxy will be installed on all servers except the LDAP servers.
- If you answer `no`, HAProxy will only be installed on the `dbconnectors`.

### 3. Install Multi-Master LDAP
Run this command to install LDAP in a multi-master configuration:
```
ansible-playbook -i inventory zxbot.carbonio_ldap.carbonio_install_mmr
```
or if a replica is already installed, run this playbook to promote it:
```
ansible-playbook -i inventory zxbot.carbonio_ldap.carbonio_promote_mmr
```


## License(s)

See [COPYING](COPYING.md) file for detail.
