# linux-adm-practise

## Project Overview

This repository contains a complete Ansible playbook for a two-VM Linux administration lab.

- `VM1` (`webservers`, `192.168.56.101`): Apache, Nginx, exporters, Filebeat
- `VM2` (`monitoring`, `192.168.56.102`): Prometheus, Grafana, ELK stack

The automation configures both infrastructure and observability in one flow.

## Playbooks

- `ansible/site.yml` - full deployment for both VM1 and VM2
- `ansible/webserver.yml` - only web tier on VM1
- `ansible/monitoring.yml` - only monitoring tier on VM2

## Roles Included

### Webserver side (VM1)

- `apache` - deploys Apache backends on ports 8081/8082/8083
- `nginx` - configures Nginx as load balancer in front of Apache
- `docker` - installs Docker and starts an Nginx container balancer
- `node_exporter` - host metrics exporter for Prometheus
- `nginx_exporter` - Nginx metrics exporter for Prometheus
- `elk` - installs Filebeat and ships Nginx logs to Logstash

### Monitoring side (VM2)

- `docker` - Docker runtime for containerized services
- `prometheus` - metrics collection and scraping
- `grafana` - dashboards and datasource setup
- `elk` - Elasticsearch + Kibana + Logstash via Docker Compose

## ELK Logging Pipeline

`Nginx -> Filebeat -> Logstash -> Elasticsearch -> Kibana`

Main ports:

- Elasticsearch: `9200`
- Kibana: `5601`
- Logstash (beats input): `5044`

## Inventory and Variables

- Inventory: `ansible/inventory/hosts.yml`
- Shared vars: `ansible/group_vars/all.yml`
- Web vars: `ansible/group_vars/webservers.yml`
- Monitoring vars: `ansible/group_vars/monitoring.yml`

## Run Examples

```bash
ansible-playbook -i ansible/inventory/hosts.yml ansible/site.yml
ansible-playbook -i ansible/inventory/hosts.yml ansible/webserver.yml
ansible-playbook -i ansible/inventory/hosts.yml ansible/monitoring.yml
```

## Expected Result

- VM1 serves web traffic through Nginx to Apache backends.
- VM2 collects metrics (Prometheus/Grafana) and logs (ELK).
- Kibana dashboards can be built from `nginx-logs-*` data.
