# README.md (Incomplete)

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
    cd /usr/helium/validator
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
* Launch Docker-compose containers 
    ```bash
    docker-compose up -d --build
    ```
    - If you receive a permission error, then you should log out/in of session, so that Docker permission can propogate to user. Confirm Docker is added with the following command:
    ```bash
    groups
    ```    
* Confirm both validator and watchtower containers are running
    ```bash
    docker-compose ps
    ```

# Inspiration

* [How to Setup Grafana and Prometheus on Linux](https://devconnected.com/how-to-setup-grafana-and-prometheus-on-linux/)

* [Prometheus Node Exporter](https://github.com/prometheus/node_exporter)
    - [Helium Miner Grafana Dashboard](https://github.com/tedder/helium_miner_grafana_dashboard)

* [Helium Miner Exporter](https://github.com/tedder/miner_exporter)