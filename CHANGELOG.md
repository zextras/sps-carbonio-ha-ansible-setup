# Changelog

All notable changes to this project will be documented in this file. 

### [25.9.0] (2025-09-30)


### Features
* Upgraded Kafka version from 3.1.1 to 4.0.0
* Replaced Zookeeper with Kafka Kraft due to the use of a new version of Kafka
* Group [zookeeper_servers] is deprecated in inventory: As of version 25.9.0, this group is deprecated for new installations, leave it empty as Zookeeper has been replaced by Kafka Kraft and will no longer be used
* Replaced deprecated Ansible module postgresql_set with postgresql_alter_system (ensures forward compatibility with community.postgresql ≥ 5.0, removes deprecation warnings)
* Updated collection dependency: now requires community.postgresql version 3.13.0 or higher, as newer module is used
* Removed "Is this a full HA installation (yes/no)?" question. This user interaction will no longer occur during installation
* Renamed High Availability to Carbonio Cluster Services Redundancy
