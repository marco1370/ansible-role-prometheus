# ansible-role-prometheus

Ansible role for deploying the prometheus binary and configure it

## How to install
### requirements.yml
**Put the file in your roles directory**
```yaml
---
- src: https://github.com/mohsenmirzaei1991/ansible-role-prometheus.git
  scm: git
  version: main
  name: ansible-role-prometheus
```

### Download the role

```Shell
ansible-galaxy install -f -r ./roles/requirements.yml --roles-path=./roles
```

## Requirements

- Ansible >= 2.10 **(No tests has been realized before this version)**

## Role Variables

All variables which can be overridden are stored in [default/main.yml](default/main.yml) file as well as in table below.

| Name           | Default Value | Choices | Description                        |
| -------------- | ------------- | ------- | -----------------------------------|
| `prometheus_version` | latest | [version](https://github.com/prometheus/prometheus/tags) | Choice of the prometheus version. |
| `prometheus_log_level` | info | debug, info, warn, error | Choice of the prometheus log level. |
| `prometheus_log_format` | logfmt | logfmt, json | Choice of the prometheus log format. |
| `prometheus_listen_address` | ':9090' |  | Address to listen on for UI, API, and telemetry. |
| `prometheus_external_url` | 'http://{{ ansible_default_ipv4.address }}{{ prometheus_listen_address }}' |  | The URL under which Prometheus is externally reachable. |
| `prometheus_extra_opts` | [] |  | Prometheus extra options. |
| `prometheus_configuration` | [] |  | Prometheus [configuration](https://prometheus.io/docs/prometheus/latest/configuration/configuration/) |
| `prometheus_web_configuration` | [] |  | Prometheus [web configuration](https://prometheus.io/docs/prometheus/latest/configuration/https/). |


## Example Playbook

```yaml
---
- hosts: all
  tasks:
    - name: Include ansible-role-prometheus
      include_role:
        name: ansible-role-prometheus
      vars:
        prometheus_version: '2.37.4'
        prometheus_extra_opts:
          - --storage.tsdb.retention.time=1d

        prometheus_web_configuration:
          tls_server_config:
            cert_file: /etc/ssl/prometheus.crt
            key_file: /etc/ssl/prometheus.key
          basic_auth_users:
            admin: admin

        prometheus_configuration:
          global:
            scrape_interval: 30s
            evaluation_interval: 10s
          scrape_configs:
            - job_name: 'prometheus'
              static_configs:
                - targets: ['localhost:9090']
```

