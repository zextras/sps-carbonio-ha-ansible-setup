# Install Multi-Master LDAP

This role installs an additional Carbonio Directory Server and configures LDAP Multi-Master Replication.

## Responsibilities

The role:

- enables Multi-Master Replication on the existing LDAP master;
- installs the Directory Server on the additional master;
- runs Carbonio pending setups and bootstrap;
- configures LDAP master URLs;
- enables Service Discover where required;
- restarts Carbonio services.

## License

GPL-3.0-only

## Author Information

Zextras  
<https://www.zextras.com>
