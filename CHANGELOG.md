# Changelog

All notable changes to this project will be documented in this file. 


### [26.6.0] (2026-06-10)


### Feature
* Added validation for inventory values (hostnames, domains, and IP addresses) to prevent misconfigurations caused by INI parsing
* Added netaddr dependency handling for inventory IP address validation

### Bug Fixes
* Fixed deprecated ansible_* facts usage by migrating to ansible_facts for compatibility with ansible-core 2.24
* Replaced ansible_facts.fqdn with inventory_hostname to avoid incorrect hostname resolution when hosts file entries are misconfigured
* Updated the `ansible.posix` dependency to version `2.2.0` to remove deprecation warnings related to the deprecated `to_native` import path in newer `ansible-core` versions
* Replaced deprecated apt_key usage with keyring-based APT repository configuration for PostgreSQL repositories
* Replaced the word "WSC" with "Chats" in the names of some tasks where necessary to avoid inconsistencies

### [26.3.1] (2026-03-24)


### Bug Fixes
* Fixed an issue where inline comments in inventory variable examples were propagated as part of the value into generated configuration files, causing invalid Postfix configuration


### [26.3.0] (2026-03-11)


### Feature
* Added new DBConnector package carbonio-videorecorder-db for DB-backed metadata storage and job management support

### Bug Fixes
* Added workaround to ensure service-discover is enabled after MMR bootstrap


### [25.12.0] (2025-12-17)


### Feature
* Implemented promotion of LDAP replica in a separate playbook (only for converting infrastructure to User Mail Replica)
* Added a check that password files exist and are located in the same folder as the inventory
* Added a check that workStreamServers group exists in the inventory


### [25.9.1] (2025-10-15)


### Bug Fixes
* Fixed carbonio.target restart condition


### [25.9.0] (2025-09-30)


### Features
* Upgraded Kafka version from 3.1.1 to 4.0.0
* Replaced Zookeeper with Kafka Kraft due to the use of a new version of Kafka
* Group [zookeeper_servers] is deprecated in inventory: As of version 25.9.0, this group is deprecated for new installations, leave it empty as Zookeeper has been replaced by Kafka Kraft and will no longer be used
* Replaced deprecated Ansible module postgresql_set with postgresql_alter_system (ensures forward compatibility with community.postgresql ≥ 5.0, removes deprecation warnings)
* Updated collection dependency: now requires community.postgresql version 3.13.0 or higher, as newer module is used
* Removed "Is this a full HA installation (yes/no)?" question. This user interaction will no longer occur during installation
* Renamed High Availability to Carbonio Cluster Services Redundancy
