# Ansible Collection - zxbot.carbonio_ldap

An ansible collection to install Multi Master LDAP part of Carbonio HA 

To install Multi Master LDAP using this collection you have to modify the masterDirectoryServers group in the inventory file. It supports only FQDN.

### Install the collection

```
ansible-galaxy collection install zxbot.carbonio_ldap
```
### Modify the inventory 

To configure the inventory for HA installation, update the **inventory file** with specific variables and add the following groups:

`masterDirectoryServers` group includes the following variable:
* `ldap_role` Specifies the LDAP role. Use master for the initial master or mmr for additional masters.
```
[masterDirectoryServers]
svc1.example.com ldap_role=master
svc2.example.com ldap_role=mmr
```

Example for Full HA inventory file

```
[kafka]
svcs1.example.com broker_id=1
svcs2.example.com broker_id=2
svcs3.example.com broker_id=3

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
- Do not assign the `master` role to any additional servers being configured as extra masters.
  
- Additional servers should be assigned:
  - `mmr` for LDAP
```

### Install Multi-Master LDAP

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
