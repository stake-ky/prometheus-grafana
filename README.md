# README.md (Incomplete)

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

# Inspiration

* [How to Setup Grafana and Prometheus on Linux](https://devconnected.com/how-to-setup-grafana-and-prometheus-on-linux/)

* [Prometheus Node Exporter](https://github.com/prometheus/node_exporter)
    - [Helium Miner Grafana Dashboard](https://github.com/tedder/helium_miner_grafana_dashboard)
    
* [Helium Miner Exporter](https://github.com/tedder/miner_exporter)