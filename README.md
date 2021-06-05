# README.md (Incomplete)

## Introduction
This is a tool to monitor the helium node perfomance. This will help to see the resources and performance data in the grafana dashboard also trigger the alert to the pagerduty using the alertmanager. We are using docker containers to run the services.

## Project Structure

```
Project
└───alertmanager
│   |   alertmanager.yml   
|
└───certbot
|   |   ...
│
└───grafana
|   └───config
|   |   └───dashboards
|   |       |   config.json
|   |
|   └───provisioning
|       └───dashboards
|           |   local.yml
|
└───nginx
|   |   ...
|
└───prometheus
│   │   first_rules.yml
│   │   prometheus.yml
│   
└───scripts
|   │   install_docker_git_etc.sh
|   │   install_ssl.sh
|
|   docker-compose.yml
|   README.md
```

## Instruction to Setup Project
* Make directory helium and change owner
    ```bash
    sudo mkdir /usr/helium && sudo chown -R $USER:$USER /usr/helium
    ```
* Change to the newly created directory    
    ```bash
    cd /usr/helium
    ```
* Clone the helium-validator repository    
    ```bash
    git clone https://github.com/stake-ky/prometheus-grafana.git
    ```
* Install Docker and Docker-compose using the script provided. Update the Distribution Version/Name and Docker and Docker-compose Versions as applicable. 
    ```bash
    cd /usr/helium/prometheus-grafana
    ```
    ```bash
    ./scripts/install_docker_git_etc.sh
    ```
* Create credentials to restrict public access to Prometheus.
    ```bash
    cd prometheus && sudo htpasswd -c .credentials admin
    ```
* Create random, complex root password variable for Grafana.
    ```bash
    RANDOM_ROOT_PASSWORD=$(head /dev/urandom | tr -dc A-Za-z0-9 | head -c 24 ; echo '') &&\
    sed -i 's+RANDOM_ROOT_PASSWORD+'${RANDOM_ROOT_PASSWORD}'+g' docker-compose.yml
    ```
* Update project URL. Change `project_url` below, before running script.
    ```bash
    PROJECT_URL=project_url &&\
    find . -type f -exec sed -i 's+example.com+'${PROJECT_URL}'+g' {} \;
    ```    

* Please follow the [Prometheus Integration Guide](https://www.pagerduty.com/docs/guides/prometheus-integration-guide) guide and generate the `Integration Key`. 

* Update the PagerDuty Integration Key in the alertmanager.yml configuration file under alertmanager directory. Replace the `PagerDutyIntegrationKey` with the `Integration Key ` generated in the previous stage. 

* Launch Docker-compose containers 
    ```bash
    docker-compose up -d --build
    ```
    - If you receive a permission error, then you should log out/in of session, so that Docker permission can propogate to user. Confirm Docker is added with the following command:
    ```bash
    groups
    ```    
* Confirm all the containers are running using below command
    ```bash
    docker-compose ps
    ```

# Inspiration

* [How to Setup Grafana and Prometheus on Linux](https://devconnected.com/how-to-setup-grafana-and-prometheus-on-linux/)

* [Prometheus Node Exporter](https://github.com/prometheus/node_exporter)
    - [Helium Miner Grafana Dashboard](https://github.com/tedder/helium_miner_grafana_dashboard)

* [Helium Miner Exporter](https://github.com/tedder/miner_exporter)

* [Prometheus Integration Guide](https://www.pagerduty.com/docs/guides/prometheus-integration-guide)