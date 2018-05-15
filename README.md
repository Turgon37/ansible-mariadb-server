Ansible Role MariaDB server
=========

[![Build Status](https://travis-ci.org/Turgon37/ansible-mariadb-server.svg?branch=master)](https://travis-ci.org/Turgon37/ansible-mariadb-server)

:warning: This role is under development, some important (and possibly breaking) changes may happend. Don't use it in production level environments but you can eventually base your own role on this one :hammer:

:grey_exclamation: Before using this role, please know that all my Ansible roles are fully written and accustomed to my IT infrastructure. So, even if they are as generic as possible they will not necessarily fill your needs, I advice you to carrefully analyse what they do and evaluate their capability to be installed securely on your servers.

**This roles configure the Mariadb server.**

## Features

Currently this role provide the following features :

  * mariadb installation
  * directory structure, global configuration
  * user and databases management
  * monitoring items for
    * Zabbix
  * [local facts](#facts)

## Requirements

### OS Family

This role is available for

  * Debian/Raspbian 8/9
  * CentOS 7

### Dependencies

If you use the zabbix monitoring profile you will need the role [ansible-zabbix-agent](https://github.com/Turgon37/ansible-zabbix-agent)


## Role Variables

The variables that can be passed to this role and a brief description about them are as follows:

| Name                                      | Types/Values      | Description                                                                                                                 |
| ------------------------------------------| ------------------|---------------------------------------------------------------------------------------------------------------------------- |
| mariadb_server__facts                     | Boolean           | Install the local fact script                                                                                               |
| mariadb_server__version                   | String            | The mariadb version to install, I advice you to fix it for each hostgroup/node                                              |
| mariadb_server__service_restartable       | Boolean           | If True, the mariadb service will be restarted automatically by ansible on config changes. Set to False for production level|
| mariadb_server__service_restart_stamp_file| String            | Path to a file that will be created when Mariadb must be restarted and ansible can allowed to restart it                    |
| mariadb_server__root_password             | String            | The root password                                                                                                           |
| mariadb_server__users                     | List of Users     | List of users to manage                                                                                                     |
| mariadb_server__databases                 | Dict of databases | Dict of database to manage                                                                                                  |                                                                                              


### Users

Each user is a dict with the following attributes

```
mariadb_server__users:
  - user: 'application-app1'
    password: '{{ vault_password }}'
    host: localhost
    priv: 'database1.*:SELECT,INSERT,UPDATE,DELETE,CREATE,DROP,INDEX,ALTER,LOCK TABLES'
    state: present
```

### Databases

Each database must be defined as a dict with the following attributes

```
mariadb_server__databases:
  database1:
    collation: utf8_unicode_ci
    encoding: utf8
    state: present
```

## Facts

By default the local fact are installed and expose the following variables :


* ```ansible_local.mariadb.version_full```
* ```ansible_local.mariadb.version_major```


## Example Playbook

To use this role create or update your playbook according the following example :


```
    - hosts: servers
      roles:
         - mariadb-server
      vars:
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
```

## License

MIT