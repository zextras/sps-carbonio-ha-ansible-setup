# Confirm HA Setup

This role validates the existing Carbonio environment before starting the LDAP Multi-Master HA setup.

It displays the installed Carbonio version, LDAP HA collection version, and configured Zextras repository before any HA configuration is applied.

## Responsibilities

The role:

- retrieves the LDAP HA collection source and version;
- retrieves the installed Carbonio version from the existing Directory Server;
- detects the configured Zextras repository on the LDAP servers;
- verifies that the same repository is configured on all LDAP servers;
- displays the detected Carbonio version, repository, and HA playbook information;
- requests confirmation before continuing with the LDAP HA setup.

## Non-Interactive Confirmation

Repository and playbook confirmation can be automated with:

```
carbonio_auto_confirm_repository_and_playbook: true
```
When enabled, the environment information is still displayed, but the interactive confirmation prompt is skipped.

## License

GPL-3.0-only

## Author Information

Zextras
https://www.zextras.com