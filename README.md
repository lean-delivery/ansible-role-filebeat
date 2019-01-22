Filebeat Role
=========
[![License](https://img.shields.io/badge/license-Apache-green.svg?style=flat)](https://raw.githubusercontent.com/lean-delivery/ansible-role-ansible-role-filebeat/master/LICENSE)
[![Build Status](https://travis-ci.org/lean-delivery/ansible-role-ansible-role-filebeat.svg?branch=master)](https://travis-ci.org/lean-delivery/ansible-role-ansible-role-filebeat)
[![Galaxy](https://img.shields.io/badge/galaxy-lean__delivery.ansible-role-filebeat-blue.svg)](https://galaxy.ansible.com/lean_delivery/ansible-role-filebeat)
![Ansible](https://img.shields.io/ansible/role/d/role_id.svg)
![Ansible](https://img.shields.io/badge/dynamic/json.svg?label=min_ansible_version&url=https%3A%2F%2Fgalaxy.ansible.com%2Fapi%2Fv1%2Froles%2Frole_id%2F&query=$.min_ansible_version)

## Summary

This role:
  - installs filebeat on Ubuntu, CentOS
  - copies prepared configuration file (log path, connect to elasticsearch etc.)


Role tasks
------------

- Prepare server (add elastic repo)
- Install filebeat
- Copy configuration file

Requirements
------------

- Minimal Version of the ansible for installation: 2.5
 - **Supported OS**:
   - CentOS
     - 6, 7
   - Ubuntu
     - 16.04, 18.04
   - Debian
     - 8, 9

## Role Variables
--------------

You can override any variable below by setting "variable: value" in playbook.

- `elastic_branch`
Is used to select main Elasticsearch branch to be installed (5.x or 6.x current stable versions). Default value is `6`.
- `elastic_gpg_key`
GPG-key from elasticsearch repository. Default value is `https://artifacts.elastic.co/GPG-KEY-elasticsearch`

- `nix_filebeat_input_logpath`
Path to log files for *NIX OS family. Default value is `"/var/log/*.log"`
- `win_filebeat_input_logpath`
Path to log files for WIN OS family. Default value is `'c:\windows\*.log'`

- `filebeat_ignore_older`
Value (any time strings like 2h, 5m can be used) above which files will be ignored. Default value is `0` (disabled)
- `filebeat_scan_frequency`
Defines how often filebeat checks file updates. Default value is `15s`
- `filebeat_harvester_buffer_size`
Defines the buffer size. Default value is `65535`

- `filebeat_node_name`
Name of the filebeat node. Default value is `{{ inventory_hostname }}`. If this options is not defined, the hostname is used.

- `filebeat_output`
Is used to configure what output to use when sending data (`elasticsearch` or `logstash`). Default value is `elasticsearch`

- `elasticsearch.host`
Array of hosts to connect to. Default value is `localhost`
- `elasticsearch.port`
Value for setting custom port. Default value is `9200`

- `logstash.host`
Array of hosts to connect to. Default value is `localhost`
- `logstash.port`
Value for setting custom port. Default value is `5044`

- `filebeat_bulk_max_size`
Maximum number of events to bulk in a single Logstash request. Default value is `500`
- `filebeat_worker`
Number of workers per Elasticsearch host. Default value is `1`

- `filebeat_ssl_enabled`
Enable SSL support. Default value is `false`
- `filebeat_cert_file`
Path to certificate for SSL client authentication Default value is `"/etc/pki/tls/certs/server.crt"`
- `filebeat_pkey_file`
Path to client certificate key. Default value is `"/etc/pki/CA/ca-root.pem"`

- `filebeat_logging_to_syslog`
Send all logging output to syslog. Default value is `false`
- `filebeat_logging_to_files`
Send all logging output to rotating files. Default value is `true`
- `filebeat_rotateeverybytes`
Defines log file size limit. Defalt value is `104857600` = `100MB`
- `filebeat_keepfiles`
Number of log files to keep. Default value is `30`
- `nix_filebeat_logpath`
Path were the logs are written for *NIX family. Default value is `"/var/log/filebeat"`
- `filebeat_logname`
Name of the logging files. Default value is `"filebeat.log"`

- `win_filebeat_logpath`
Path were the logs are written for *NIX family. Default value is `'c:\programdata\filebeat\logs'`



Dependencies
------------

None.

Example Playbook
----------------

### Installing Filebeat 6.x version:

```yaml
- name: Install filebeat
  hosts: all
  roles:
    - role: ansible-role-filebeat
```
### Installing Filebeat 6.x version with custom path to log files and elasticsearch output:

```yaml
- name: Install filebeat
  hosts: all
  roles:
    - role: ansible-role-filebeat
  vars:
    nix_filebeat_input_logpath: "/var/log/messages"
    elasticsearch:
      host: elasticsearch.example.com
      port: 9200
```

License
-------
Apache

Author Information
------------------

authors:
  - Lean Delivery Team <team@lean-delivery.com>
