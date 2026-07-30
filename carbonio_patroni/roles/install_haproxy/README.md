#### `carbonio_patroni/roles/install_replica_postgres/README.md`

```markdown
# Install Replica PostgreSQL

This role installs and starts PostgreSQL on the additional database nodes before Patroni configuration.

## Responsibilities

The role:

- configures PostgreSQL repositories;
- installs PostgreSQL packages;
- prepares the PostgreSQL service;
- starts PostgreSQL on additional database nodes.

## License

GPL-3.0-only

## Author Information

Zextras  
<https://www.zextras.com>
```

#### `carbonio_patroni/roles/install_patroni/README.md`

```markdown
# Install Patroni

This role installs and configures Patroni for PostgreSQL high availability.

## Responsibilities

The role:

- configures PostgreSQL for Patroni;
- installs Patroni and its dependencies;
- creates the Patroni configuration;
- configures the PostgreSQL cluster;
- starts the Patroni service.

## License

GPL-3.0-only

## Author Information

Zextras  
<https://www.zextras.com>
```

#### `carbonio_patroni/roles/install_haproxy/README.md`

```markdown
# Install HAProxy

This role installs and configures HAProxy for Carbonio Cluster Services Redundancy.

## Responsibilities

The role:

- installs HAProxy;
- creates the HAProxy configuration;
- starts and enables the HAProxy service;
- configures Carbonio nodes to use HAProxy for LDAP connections;
- restarts Application Server services where required.

## License

GPL-3.0-only

## Author Information

Zextras  
<https://www.zextras.com>
```

#### `carbonio_patroni/roles/move_dbconnectors/README.md`

```markdown
# Move Database Connectors

This role moves Carbonio database connector components to the configured DB Connector nodes.

## Responsibilities

The role:

- installs database connector packages on the target nodes;
- removes obsolete packages from PostgreSQL nodes;
- removes obsolete Preview DB connector packages;
- runs Carbonio pending setups.

## License

GPL-3.0-only

## Author Information

Zextras  
<https://www.zextras.com>
```

#### `carbonio_patroni/roles/pre_installation_checks/README.md`

```markdown
# Pre-installation Checks

This role validates the inventory and required configuration before PostgreSQL and Patroni installation.

## Responsibilities

The role:

- validates required inventory groups;
- checks inventory hostnames and configuration values;
- validates Video Server IP addresses;
- verifies required password files;
- stops the playbook when validation fails.

## License

GPL-3.0-only

## Author Information

Zextras  
<https://www.zextras.com>
```
