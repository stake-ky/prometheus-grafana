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
└───src
|   |   ...
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
    cd /usr/helium/prometheus-grafana && RANDOM_ROOT_PASSWORD=$(head /dev/urandom | tr -dc A-Za-z0-9 | head -c 24 ; echo '') &&\
    sed -i 's+RANDOM_ROOT_PASSWORD+'${RANDOM_ROOT_PASSWORD}'+g' docker-compose.yml
    ```
* Update project URL. Change `project_url` below, before running script.
    ```bash
    PROJECT_URL=project_url && find . -type f -exec sed -i 's+example.com+'${PROJECT_URL}'+g' {} \;
    ```    

## Integrate PagerDuty
* Update the PagerDuty Integration Key in the `alertmanager.yml` configuration file under alertmanager directory. Replace the `PAGERDUTY_INTEGRATION_KEY` with the `Integration Key` obtained from Automation -> Event Rules -> Default Global Ruleset in [PagerDuty](https://stakeky.pagerduty.com/rules/rulesets/_default)

    ```bash
    PAGERDUTY_INTEGRATION_KEY=integration_key && find alertmanager/alertmanager.yml -type f -exec sed -i 's+PAGERDUTY_INTEGRATION_KEY+'${PAGERDUTY_INTEGRATION_KEY}'+g' {} \;
    ```    
    - Additional information can be found in the [Prometheus Integration Guide](https://www.pagerduty.com/docs/guides/prometheus-integration-guide). 

## Add Targets to Prometheus

* Update the following in `prometheus/prometheus.yml` for each target you want Prometheus to scrape:
    ```bash
      - job_name: '$VALIDATOR' # Update to reflect the name of the validator want to scrape
    # metrics_path: /metrics # Defaults to '/metrics'
    # scrape_interval: 5s
    scheme: https # Defaults to 'http'
    basic_auth:
      username: admin
      password: $MYCOMPLEXPASSWORD
    static_configs:
    - targets: ['node.example.com', 'miner.example.com']
    ```
    - Update `$VALIDATOR` and `targets` as well as `username` and `password` for each source.

## Installing SSL
* Open ports 80 and 443 on your server
* Install Let's Encrypt SSL using script 
    ```bash
    cd /usr/helium/prometheus-grafana
    ```
    ```bash
    ./scripts/install_ssl.sh
    ```
    - If you receive a permission error, then you should log out/in of session, so that Docker permission can propogate to user. Confirm Docker is added with the following command:
    ```bash
    groups
    ```
* Stop running Nginx container
    ```bash
    docker-compose stop nginx
    ```
* Switch to Nginx SSL config
    ```bash
    rm nginx/default.conf && cp nginx/default_https.conf.template nginx/default.conf
    ```
* Recreate Nginx
    ```bash
    docker-compose up --build --force-recreate --no-deps -d nginx
    ```

## Launch Project
* Launch Docker-compose containers 
    ```bash
    docker-compose up -d --build
    ```
* Confirm all the containers are running using below command
    ```bash
    docker-compose ps
    ```

## Add Prometheus as Data Source in Grafana

* Navigate to `/datasources` in [Grafana Admin](https://admin.example.com/datasources) and click on `Add data source'
    
    ![Grafana Add Data Source](src/prometheus_add_data_source.png)

* Add the configuration details for your Prometheus instance

    ![Grafana Configuration](src/prometheus_config.png)

* Save & Test the configuration

    ![Save & Test Configuration](src/prometheus_config_success.png)

# Inspiration

* [How to Setup Grafana and Prometheus on Linux](https://devconnected.com/how-to-setup-grafana-and-prometheus-on-linux/)

* [Prometheus Node Exporter](https://github.com/prometheus/node_exporter)
    - [Helium Miner Grafana Dashboard](https://github.com/tedder/helium_miner_grafana_dashboard)

* [Helium Miner Exporter](https://github.com/tedder/miner_exporter)

* [Prometheus Integration Guide](https://www.pagerduty.com/docs/guides/prometheus-integration-guide)