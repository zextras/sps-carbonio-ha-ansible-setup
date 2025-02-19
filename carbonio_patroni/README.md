# Ansible Collection - zxbot.carbonio_patroni

An ansible collection to install Patroni part of Carbonio HA 

To install Kafka and Zookeeper using this collection you have to insert new groups in the inventory file It supports only FQDN.

### Install the collection

```
ansible-galaxy collection install zxbot.carbonio_patroni
```
### Modify the inventory 

To configure the inventory for HA installation, update the **inventory file** with specific variables and add the following groups:

`postgresServers` group includes the following variables:
* `postgres_version` Specifies the PostgreSQL version used for PostgreSQL HA.
* `patroni_role` Specifies the Patroni role. Use primary for the initial master or secondary for additional masters.
```
[postgresServers]
svc1.example.com postgres_version=16 patroni_role=primary
svc2.example.com postgres_version=16 patroni_role=secondary
```

`dbsConnectorServers` group specifies db connectors for HA (it will move connectors from postgres to application servers)
```
[dbsConnectorServers]
mbox1.example.com 
mbox2.example.com
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

### Important Notes on Initial Roles for HA Configuration

The initial roles assigned during the standard installation must remain on the servers configured in the standard environment. Follow these guidelines:

```plaintext
- Do not assign  the `primary` role to any additional servers being configured as extra masters.
  
- Additional servers should be assigned:
  - `secondary` for PostgreSQL
```

###  Install PostgreSQL HA

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


## License(s)

See [COPYING](COPYING.md) file for detail.
