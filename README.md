# Prometheus monitoring demo site

[![Build Status](https://circleci.com/gh/prometheus/demo-site.svg?style=svg)](https://circleci.com/gh/prometheus/demo-site)
[![License](https://img.shields.io/badge/license-Apache%20License-brightgreen.svg)](https://opensource.org/licenses/Apache-2.0)
[![IRC](https://img.shields.io/badge/chat-on%20freenode-blue.svg)](http://webchat.freenode.net/?channels=prometheus)

## [demo.prometheus.io](https://demo.prometheus.io)

This repository provides a demo site for [prometheus](https://github.com/prometheus/prometheus), [alertmanager](https://github.com/prometheus/alertmanager), prometheus exporters, and [grafana](https://github.com/grafana/grafana).
Site is provisioned with ansible running every day and on all commits to master branch. Everything is fully automated with travis ci pipeline. If you want to check `ansible-playbook` output, go to [last build](https://app.circleci.com/pipelines/github/prometheus/demo-site).

Have a look at configuration files in [group_vars/](group_vars).

## Applications

All applications should be running on their default ports.

| App name          | Address (HTTP)                                       | Address (HTTPS)                                           |
|-------------------|------------------------------------------------------|-----------------------------------------------------------|
| node_exporter     | [demo.prometheus.io:9100][node_exporter_http]     | [node.demo.prometheus.io][node_exporter_https]         |
| prometheus        | [demo.prometheus.io:9090][prometheus_http]        | [prometheus.demo.prometheus.io][prometheus_https]      |
| alertmanager      | [demo.prometheus.io:9093][alertmanager_http]      | [alertmanager.demo.prometheus.io][alertmanager_https]  |
| grafana           | [demo.prometheus.io:3000][grafana_http]           | [grafana.demo.prometheus.io][grafana_https]            |

## Important notice

Before running, golang is required to be installed on deployer machine (necessary to install random_exporter).

Most services can be accessed in two ways (links in [Applications](#Applications) section. As an example, prometheus can be accessed via:
  - **http**://demo.prometheus.io:9090 - default way
  - **https**://prometheus.demo.prometheus.io - workaround which in background communicates with prometheus via insecure, "default" channel mentioned above

This workaround was needed to solve issue [cloudalchemy/demo-site#13](https://github.com/cloudalchemy/demo-site/issues/13).

## Run yourself

You can easily run such setup yourself without much knowledge how any part of this works. You just need to do two things:

#### Change ansible inventory

First of all you need to configure your inventory, ours is located in [`hosts`](hosts) file. Here you set up your target hosts by changing value of `ansible_host` variable. Also here you can exclude parts of this demo site, so if you don't need our website, you just remove this part:

```
[web]
demo
```

Accordingly you can exclude grafana, prometheus.

#### Change passwords

For security measures we encrypted some of our passwords, but it is easy to use yours! You can do it by replacing a file located at [`group_vars/grafana/vault`](group_vars/grafana/vault) with following content:

```
vault_grafana_password: <<INSERT_YOUR_GRAFANA_PASSWORD>>
```

#### Download the 'random' exporter binary

You will have to manually run `go` command to download & copy the [`random`](https://github.com/prometheus/client_golang/tree/master/examples/random) exporter binary to [`playbooks/files`](playbooks/files) directory.

- The binary will be downloaded at `GOPATH` location. The value of `GOPATH` can be found by running `go env|grep GOPATH` command on your system.

```
go get -u github.com/prometheus/client_golang/examples/random
cp <GOPATH>/bin/random /path/to/demo-site/playbooks/files/
```

#### Run as usual Ansible playbook

```bash
# Download roles
ansible-galaxy install -r roles/requirements.yml

# Run playbook
ansible-playbook site.yml
# or when using vault encrypted variables
ansible-playbook --vault-id @prompt site.yml
```

# 

demo site is deployed using [Prometheus Community](https://github.com/prometheus-community/ansible) ansible roles.

[node_exporter_http]: http://demo.prometheus.io:9100
[node_exporter_https]: https://node.demo.prometheus.io

[prometheus_http]: http://demo.prometheus.io:9090
[prometheus_https]: https://prometheus.demo.prometheus.io

[alertmanager_http]: http://demo.prometheus.io:9093
[alertmanager_https]: https://alertmanager.demo.prometheus.io

[grafana_http]: http://demo.prometheus.io:3000
[grafana_https]: https://grafana.demo.prometheus.io
