# Confirm HA Setup

This role validates and confirms the Carbonio Kafka playbook version before starting the HA setup.

## Responsibilities

The role:

- retrieves the Kafka collection source and version;
- displays the detected playbook information;
- requests confirmation before continuing with the Kafka HA setup.

## Non-Interactive Confirmation

Playbook confirmation can be automated with:

```
carbonio_auto_confirm_playbook: true
```

When enabled, the playbook information is still displayed, but the interactive confirmation prompt is skipped.

## License

GPL-3.0-only

## Author Information

Zextras
https://www.zextras.com