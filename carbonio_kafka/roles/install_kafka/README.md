# Install Kafka

This role installs and configures Apache Kafka for Carbonio Cluster Services Redundancy.

It prepares the required system user and directories, installs Java, configures Kafka in KRaft mode, and starts the Kafka service.

## Responsibilities

The role:

- installs Java;
- creates the Kafka system user and directories;
- downloads or uploads the Kafka archive;
- configures Kafka in KRaft mode;
- creates the Kafka systemd service;
- starts and enables Kafka;
- removes the installation archive.

## License

GPL-3.0-only

## Author Information

Zextras  
<https://www.zextras.com>