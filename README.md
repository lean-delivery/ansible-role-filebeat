Filebeat Role
=========
[![License](https://img.shields.io/badge/license-Apache-green.svg?style=flat)](https://raw.githubusercontent.com/lean-delivery/ansible-role-filebeat/master/LICENSE)
[![Build Status](https://travis-ci.org/lean-delivery/ansible-role-filebeat.svg?branch=master)](https://travis-ci.org/lean-delivery/ansible-role-filebeat)
[![Build Status](https://gitlab.com/lean-delivery/ansible-role-filebeat/badges/master/build.svg)](https://gitlab.com/lean-delivery/ansible-role-filebeat/pipelines)
[![Galaxy](https://img.shields.io/badge/galaxy-lean__delivery.filebeat-blue.svg)](https://galaxy.ansible.com/lean_delivery/filebeat)
![Ansible](https://img.shields.io/ansible/role/d/38385.svg)
![Ansible](https://img.shields.io/badge/dynamic/json.svg?label=min_ansible_version&url=https%3A%2F%2Fgalaxy.ansible.com%2Fapi%2Fv1%2Froles%2F38385%2F&query=$.min_ansible_version)


## Summary


This role:
  - installs filebeat on Ubuntu, CentOS, Windows
  - copies prepared configuration file (log path, connect to elasticsearch etc.)




Role tasks
------------


- Prepare server (add elastic repo)
- [Optional] Create folder(s) for custom paths
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
   - Windows


## Role Variables
--------------


You can override any variable below by setting "variable: value" in playbook.


- `filebeat_version`
Is used to select main Filebeat branch to be installed (5.x or 6.x current stable versions). Default value is `6`.
- `filebeat_last_version`
Is used to select specific Filebeat version to be installed. Default value is `6.6.0`
- `elastic_gpg_key`
GPG-key from elasticsearch repository. Default value is `https://artifacts.elastic.co/GPG-KEY-elasticsearch`
- `filebeat_node_name`
Name of the filebeat node. Default value is `{{ inventory_hostname }}`. If this options is not defined, the hostname is used.
- `filebeat_ssl_enabled`
Turns on/off SSL connection between filebeat and logstash/elasticsearch. SSL options should be set by corresponding dict fields like shown below:
```
  ssl:
    key: "/etc/pki/tls/private/server.key"
    certificate: "/etc/pki/tls/certs/server.crt"
    certificate_authorities: "/etc/pki/CA/ca-root.pem"
```
 in case of Windows setup:   
 ```
  ssl:
    key: 'c:\tls\private\server.key'
    certificate: 'c:\tls\certs\server.pem'
    certificate_authorities: 'c:\CA\ca-root.pem'
```


The `path` section of the configuration options defines where Filebeat looks for its files. For example, Filebeat looks for the Elasticsearch template file in the configuration path and writes log files in the logs path. Filebeat looks for its registry files in the data path. Default values for Linux host are set up this way:
```
path:
  home: /usr/share/filebeat
  config: /etc/filebeat
  data: /var/lib/filebeat
  logs: /var/log/filebeat
```
in case of Windows setup default paths look like:
```
path:
  home: 'c:\program files\filebeat'
  config: 'c:\program files\filebeat'
  data: 'c:\programdata\filebeat'
  logs: 'c:\programdata\filebeat\logs'
```
- `win_download_path`
Temp directory for Windows to download and upzip Filebeat package. Default value is `'{{ ansible_env.TEMP }}/filebeat'` (ansible_env.TEMP value solves idempotence issue)


- `input_logpath` 
Path to log files. 

Default value for *NIX OS family is `"/var/log/*.log"`

Default value for WIN OS family is `'c:\windows\*.log'`

Variable `filebeat_inputs` defines type of logs that will be processed by pipeline, their log paths and Elasticsearch index that should store this type of logs. 
You can specify several inputs with various paths, logtypes and index names using yaml format like in example below:
```
    filebeat_inputs:
      - name: hybris
        paths: 
          - '/var/log/console*.log'
        fields:
          logtype: hybris
          index_name: hybris-console
      - name: access
        paths: 
          - '/var/log/access*.log'
          - '/var/log/nginx_access*.log'
        fields:
          logtype: access
          index_name: nginx-access
```


## Output customization:
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


## Advanced config parameters:


The `filebeat(systemd)\initd` section of the configuration  options defines which init script will be used to manage filebeat service depending on the *nix OS. Custom paths will be taken into account (if configured).
- `filebeat_service_name`
Name of nssm\init script, which manages filebeat service


- `filebeat_bulk_max_size`
Maximum number of events to bulk in a single Logstash request. Default value is `500`
- `filebeat_worker`
Number of workers per Elasticsearch host. Default value is `1`
- `filebeat_logging_to_syslog`
Send all logging output to syslog. Default value is `false`
- `filebeat_logging_to_files`
Send all logging output to rotating files. Default value is `true`
- `filebeat_rotateeverybytes`
Defines log file size limit. Defalt value is `104857600` = `100MB`
- `filebeat_keepfiles`
Number of log files to keep. Default value is `30`
- `filebeat_ignore_older`
Value (any time strings like 2h, 5m can be used) above which files will be ignored. Default value is `0` (disabled)
- `filebeat_scan_frequency`
Defines how often filebeat checks file updates. Default value is `15s`
- `filebeat_harvester_buffer_size`
Defines the buffer size. Default value is `65535`
- `filebeat_logname`
Name of the logging files. Default value is `"filebeat.log"`


## Dependencies
------------


ca-cert (only for installation with SSL)


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
    input_logpath: "/var/log/messages"
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
