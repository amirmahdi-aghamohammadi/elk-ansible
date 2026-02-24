# ELK Stack Ansible Deployment

**ELK Ansible Deployment** is a fully modular, secure, and idempotent Ansible project to deploy the **ELK Stack (Elasticsearch + Logstash + Kibana)** using **Docker** and **Docker Compose** on Ubuntu servers.

It installs Docker, deploys ELK via templated `docker-compose`, manages Logstash configuration, supports encrypted secrets using Ansible Vault, and safely restarts services when configuration changes.

![Ansible](https://img.shields.io/badge/ansible-8.x+-blue)
![Docker](https://img.shields.io/badge/docker-ready-blue)
![Elasticsearch](https://img.shields.io/badge/elasticsearch-7.x-green)
![Logstash](https://img.shields.io/badge/logstash-7.x-orange)
![Kibana](https://img.shields.io/badge/kibana-7.x-yellow)
![License](https://img.shields.io/badge/license-MIT-brightgreen)





<div align="center">

<img src="https://www.docker.com/wp-content/uploads/2022/03/Moby-logo.png" width="120"/>
<img src="https://www.vectorlogo.zone/logos/elastic/elastic-icon.svg" width="120"/>
<img src="https://www.vectorlogo.zone/logos/elasticco_logstash/elasticco_logstash-icon.svg" width="120"/>
<img src="https://www.vectorlogo.zone/logos/elasticco_kibana/elasticco_kibana-icon.svg" width="120"/>
</div>



## Stack Overview

This project deploys:

* **Elasticsearch** – Distributed search & analytics engine
* **Logstash** – Log processing & pipeline engine
* **Kibana** – Visualization & analytics UI
* **Docker** – Container runtime
* **Ansible** – Infrastructure automation



# Project Structure

```
ansible-elk/
├── ansible.cfg
├── inventory.ini
├── playbook.yml
├── requirements.txt
├── group_vars/
│   └── all.yml
├── roles/
│   ├── docker_install/
│   ├── elk_setup/
│   └── elk_restart/
└── vault/
    └── secrets.yml
```

### Roles Explanation

###  docker_install

* Installs Docker CE
* Installs Docker Compose v2 plugin
* Adds remote user to docker group

###  elk_setup

* Creates ELK directory
* Renders `docker-compose.yml` from template
* Renders `logstash.conf` from template
* Uses variables from `group_vars`
* Notifies handlers if config changes

###  elk_restart

* Restarts ELK stack when needed
* Supports rebuild tag execution



# What You Can Do

* Install Docker automatically
* Deploy full ELK stack with one command
* Use templated docker-compose
* Encrypt sensitive variables using Ansible Vault
* Automatically restart ELK when config changes
* Scale to multiple servers via inventory
* Safe & idempotent — rerun anytime



# Setup Guide

##  Clone the repository

```bash
git clone https://github.com/amirmahdi-aghamohammadi/ansible-elk.git
cd ansible-elk
```



##  Install Dependencies

Create virtual environment (recommended):

```bash
python3 -m venv venv-ansible
source venv-ansible/bin/activate
```

Upgrade pip & install requirements:

```bash
pip install --upgrade pip setuptools wheel
pip install -r requirements.txt
```

Install required Ansible collections:

```bash
ansible-galaxy collection install community.docker community.general --force
```



##  Configure Inventory

Edit `inventory.ini`:

```ini
[elk]
elksrv ansible_host=SERVER_IP ansible_user=ubuntu
```



##  Configure Public Variables

Edit:

```
group_vars/all.yml
```

Example:

```yaml
elk_dir: /opt/elk
elasticsearch_data_dir: "{{ elk_dir }}/elasticsearch-data"
logstash_pipeline_dir: "{{ elk_dir }}/logstash/pipeline"
elasticsearch_image: docker.elastic.co/elasticsearch/elasticsearch:7.17.0
logstash_image: docker.elastic.co/logstash/logstash:7.17.0
kibana_image: docker.elastic.co/kibana/kibana:7.17.0
elasticsearch_port: 9200
logstash_port: 8080
kibana_port: 5601
elasticsearch_java_opts: "-Xms1g -Xmx1g"
kibana_public_base_url: "https://domain.com"
rebuild_on_change: true
```



#  Configure Secrets (Ansible Vault)

This project supports encrypted secrets using Ansible Vault.

## Create encrypted secrets file

```bash
ansible-vault create vault/secrets.yml
```

Add secure values like:

```yaml
elastic_password: your_strong_password_here
kibana_system_password: another_secure_password
```

Save and exit.



## Edit secrets later

```bash
ansible-vault edit vault/secrets.yml
```



#  Deploy ELK Stack

## Basic deployment

```bash
ansible-playbook -i inventory.ini playbook.yml --ask-vault-pass
```



## Force rebuild (if templates changed)

```bash
ansible-playbook -i inventory.ini playbook.yml --ask-vault-pass --tags rebuild
```



# After Deployment

Check containers:

```bash
docker ps
```

Access services:

* Elasticsearch → `http://SERVER_IP:9200`
* Kibana → `http://SERVER_IP:5601`

Test:

```bash
curl http://SERVER_IP:9200
```



# How It Works Internally

1. Docker installed (if missing)
2. ELK directory created
3. `docker-compose.yml` rendered from template
4. Logstash config rendered
5. Containers started using `docker compose up -d`
6. Handler restarts stack if configuration changes
7. Optional rebuild using tag

# Requirements

* Ubuntu 20.04 / 22.04
* Python 3.8+
* Ansible 8.x+
* SSH access to target server



# Why This Project?

* Modular structure
* Clean role separation
* Vault-ready
* Production-oriented
* Easy to maintain
* Easy to scale

