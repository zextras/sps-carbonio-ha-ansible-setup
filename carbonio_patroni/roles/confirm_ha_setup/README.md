# Confirm HA Setup

This role validates the existing Carbonio environment before starting the PostgreSQL redundancy or Patroni HA setup.

It displays the installed Carbonio version and Patroni HA collection version before any HA configuration is applied.

For the Patroni HA setup, the role also validates the configured Zextras repository on the current and target DB connector servers.

## Responsibilities

The role:

- retrieves the Patroni HA collection source and version;
- retrieves the installed Carbonio version from the current PostgreSQL server;
- validates the configured Zextras repository when DB connectors are moved;
- verifies that the same repository is configured on the current and target DB connector servers;
- displays the detected Carbonio version, repository, and HA playbook information;
- requests confirmation before continuing with the HA setup.

## Repository Validation

Repository validation is enabled when `carbonio_repository_check_hosts` is defined.

The PostgreSQL replica installation does not require repository validation.

The Patroni HA setup validates the repository on:

- the current DB connector node (`postgresServers[0]`);
- the target `dbsConnectorServers`.

## Non-Interactive Confirmation

HA setup confirmation can be automated with:

```
carbonio_auto_confirm_repository_and_playbook: true
```

When enabled, the environment information is still displayed, but the interactive confirmation prompt is skipped.

## License

GPL-3.0-only

## Author Information

Zextras
https://www.zextras.com