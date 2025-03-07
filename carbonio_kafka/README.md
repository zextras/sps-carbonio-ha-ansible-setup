# Install Carbonio Ansible role

An ansible role to install Zextras Carbonio infrastructures

To install  Carbonio using this role you have to insert information in the inventory file It supports only FQDN.
All VMs must be configured and the Zextras repo to be used already set.


# Install dependencies

ansible-galaxy install -r requirements.yml

Multi master for directory server is not supported.  
__Postgres server cannot be installed on dbsConnectorServers__


Example for inventory file

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

## License(s)

See [COPYING](COPYING.md) file for detail.
