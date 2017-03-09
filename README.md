filebeat
========

Ansible role which helps to install and configure Elastic Filebeat.

The configuration of the role is done in such way that it should not be
necessary to change the role for any kind of configuration. All can be
done either by changing role parameters or by declaring completely new
configuration as a variable. That makes this role absolutely
universal. See the examples below for more details.

Please report any issues or send PR.


Examples
--------

```
---

- name: Example of how to install Filebeat with default configuration
  hosts: all
  roles:
    - filebeat

- name: Example of how to add new output
  hosts: all
  vars:
    # What to monitor
    filebeat_config_filebeat_prospectors__custom:
      - input_type: log
        document_type: apache
        paths:
          - /var/log/httpd/*_log
    # Add second output to Logstash
    filebeat_config_output__custom:
      logstash:
        index: filebeat
        hosts:
          - 127.0.0.1:5044
   roles:
    - filebeat

- name: Example of how to set output only to Logstash
  hosts: all
  vars:
    # What to monitor
    filebeat_config_filebeat_prospectors__custom:
      - input_type: log
        document_type: apache
        paths:
          - /var/log/httpd/*_log
    # Redefine the output to be only for Logstash
    filebeat_config_output:
      logstash:
        index: filebeat
        hosts:
          - 127.0.0.1:5044
   roles:
    - filebeat
```

This role doesn't allow to update the Filebeat index templates
(`/etc/filebeat/filebeat.template*.json`) for Elasticsearch. Please let me
know if such feature would be useful for you and I might implement it.


Role variables
--------------

```
# Package to be installed (explicit version can be specified here)
filebeat_pkg: filebeat

# Location of the config file
filebeat_config_file: /etc/filebeat/filebeat.yml

# YUM repo URL
filebeat_yum_repo_url: https://artifacts.elastic.co/packages/5.x/yum

# YUM repo GPG key
filebeat_yum_repo_key: https://artifacts.elastic.co/GPG-KEY-elasticsearch

# Extra EPEL YUM repo params
filebeat_yum_repo_params: {}

# Name of the service
filebeat_service: filebeat


# Default paths of the filebeat prospectors
filebeat_config_filebeat_prospectors_paths:
  - /var/log/*.log

# Default filebeat prospectors
filebeat_config_filebeat_prospectors__default:
  - input_type: log
    paths: "{{ filebeat_config_filebeat_prospectors_paths }}"

# Custom filebeat prospectors
filebeat_config_filebeat_prospectors__custom: []

# Final filebeat prospectors
filebeat_config_filebeat_prospectors: "{{
  filebeat_config_filebeat_prospectors__default +
  filebeat_config_filebeat_prospectors__custom }}"


# Default paths of the filebeat
filebeat_config_filebeat__default:
  prospectors: "{{ filebeat_config_filebeat_prospectors }}"

# Custom filebeat
filebeat_config_filebeat__custom: {}

# Final filebeat
filebeat_config_filebeat: "{{
  filebeat_config_filebeat__default.update(
  filebeat_config_filebeat__custom) }}{{
  filebeat_config_filebeat__default }}"


# List of hosts of the output elasticsearch
filebeat_config_output_elasticsearch_hosts:
  - localhost:9200

# Default output elasticsearch
filebeat_config_output_elasticsearch__default:
  hosts: "{{ filebeat_config_output_elasticsearch_hosts }}"

# Custom output elasticsearch
filebeat_config_output_elasticsearch__custom: {}

# Final output elasticsearch
filebeat_config_output_elasticsearch: "{{
  filebeat_config_output_elasticsearch__default.update(
  filebeat_config_output_elasticsearch__custom) }}{{
  filebeat_config_output_elasticsearch__default }}"


# Default output
filebeat_config_output__default:
  elasticsearch: "{{ filebeat_config_output_elasticsearch }}"

# Custom output
filebeat_config_output__custom: {}

# Final output
filebeat_config_output: "{{
  filebeat_config_output__default.update(
  filebeat_config_output__custom) }}{{
  filebeat_config_output__default }}"


# Default configuration
filebeat_config__default:
  filebeat: "{{ filebeat_config_filebeat }}"
  output: "{{ filebeat_config_output }}"

# Custom configuration
filebeat_config__custom: {}

# Final configuration
filebeat_config: "{{
  filebeat_config__default.update(
  filebeat_config__custom) }}{{
  filebeat_config__default }}"
```


Dependencies
------------

- [`config_encoder_filters`](https://github.com/jtyr/ansible-config_encoder_filters)
- [`elasticsearch`](https://github.com/jtyr/ansible-elasticsearch) (optional)
- [`kibana`](https://github.com/jtyr/ansible-kibana) (optional)
- [`logstash`](https://github.com/jtyr/ansible-logstash) (optional)


License
-------

MIT


Author
------

Jiri Tyr
