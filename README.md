Ansible Role MariaDB server
========

[![Build Status](https://travis-ci.org/Turgon37/ansible-mariadb-server.svg?branch=master)](https://travis-ci.org/Turgon37/ansible-mariadb-server)
[![License](https://img.shields.io/badge/license-MIT%20License-brightgreen.svg)](https://opensource.org/licenses/MIT)
[![Ansible Role](https://img.shields.io/badge/ansible%20role-Turgon37.mariadb_server-blue.svg)](https://galaxy.ansible.com/Turgon37/mariadb_server/)

## Description

:grey_exclamation: Before using this role, please know that all my Ansible roles are fully written and accustomed to my IT infrastructure. So, even if they are as generic as possible they will not necessarily fill your needs, I advice you to carrefully analyse what they do and evaluate their capability to be installed securely on your servers.

This roles install and configures Mariadb server.

## Requirements

Require Ansible >= 2.4

### Dependencies

If you use the zabbix monitoring profile you will need the role [turgon37.zabbix_agent](https://github.com/Turgon37/ansible-zabbix-agent)
If you use the prometheus monitoring profile you will need the role [turgon37.prometheus_exporter](https://github.com/Turgon37/ansible-prometheus-exporter)

## OS Family

This role is available for Debian and CentOS

## Features

This role currently provide :

  * mariadb installation
  * directory structure, global configuration
  * user and databases management
  * monitoring items for
    * Zabbix
  * [local facts](#facts)

## Configuration

### Role

All variables which can be overridden are stored in [defaults/main.yml](defaults/main.yml) file as well as in table below. To see default values please refer to this file.

| Name                                      | Types/Values      | Description                                                                                                                 |
| ------------------------------------------| ------------------|---------------------------------------------------------------------------------------------------------------------------- |
| mariadb_server__facts                     | Boolean           | Install the local fact script                                                                                               |
| mariadb_server__version                   | String            | The mariadb version to install, I advice you to fix it for each hostgroup/node                                              |
| mariadb_server__service_restartable       | Boolean           | If True, the mariadb service will be restarted automatically by ansible on config changes. Set to False for production level|
| mariadb_server__service_restart_stamp_file| String            | Path to a file that will be created when Mariadb must be restarted and ansible can allowed to restart it                    |
| mariadb_server__root_password             | String            | The root password                                                                                                           |
| mariadb_server__users                     | List of Users     | List of users to manage                                                                                                     |
| mariadb_server__databases                 | Dict of databases | Dict of database to manage                                                                                                  |                                                                                              


#### Users

Each user is a dict with the following attributes

```
mariadb_server__users:
  - user: 'application-app1'
    password: '{{ vault_password }}'
    host: localhost
    priv: 'database1.*:SELECT,INSERT,UPDATE,DELETE,CREATE,DROP,INDEX,ALTER,LOCK TABLES'
    state: present
```

#### Databases

Each database must be defined as a dict with the following attributes

```
mariadb_server__databases:
  database1:
    collation: utf8_unicode_ci
    encoding: utf8
    state: present
```


## Facts

I deliver the following facts with this role. This is explicitly NOT intended to be used within your ansible run as it will only work on your second run. Its intention is to make querying versions per server.

* ```ansible_local.mariadb.version_full```
* ```ansible_local.mariadb.version_major```


## Example

### Playbook

Use it in a playbook as follows:

```yaml
- hosts: all
  roles:
    - turgon37.mariadb_server
```

### Inventory

Some examples:

* Simple configuration with little tuning

```
mariadb_server__clients_prompt: '[app:01]'
mariadb_server__users:
  - user: 'application-app1'
    password: '{{ vault_password }}'
    host: localhost
    priv: 'database1.*:SELECT,INSERT,UPDATE,DELETE,CREATE,DROP,INDEX,ALTER,LOCK TABLES'
    state: present

mariadb_server__databases:
  database1:
    collation: utf8_unicode_ci
    encoding: utf8
    state: present

mariadb_server__version: 10.1
mariadb_server__datadir: /data/mysql/bases
mariadb_server__innodb_data_home_dir: /data/mysql/innodb
mariadb_server__log_dir: /data/mysql/logs
mariadb_server__skip_networking: true

mariadb_server__root_password: '{{ vault_zabbix01_mariadb_password_root }}'

mariadb_server__log_queries_not_using_indexes: false
mariadb_server__query_cache_type: 1
mariadb_server__query_cache_size: 10M

mariadb_server__tuning_group_parameters:
  thread_cache_size: 36
```
