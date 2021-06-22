UEne9POl:71r19QWp@172.31.17.217:4500

172.31.17.217:4500 172.31.28.93:4500 
UEne9POl:OrNNBVzZiNIRwbkBw0KdsU8S2SKXHQRL@172.31.17.217:4500,172.31.28.93:4500

// Prometheus
wget https://github.com/prometheus/prometheus/releases/download/v2.28.0-rc.0/prometheus-2.28.0-rc.0.linux-amd64.tar.gz
sudo tar xf prometheus-2.28.0-rc.0.linux-amd64.tar.gz

cd prometheus-2.28.0-rc.0.linux-amd64
sudo ./prometheus --config.file=./prometheus.yml &


// Foundationdb
wget https://www.foundationdb.org/downloads/6.3.15/ubuntu/installers/foundationdb-clients_6.3.15-1_amd64.deb
wget https://www.foundationdb.org/downloads/6.3.15/ubuntu/installers/foundationdb-server_6.3.15-1_amd64.deb
sudo dpkg -i foundationdb-clients_6.3.15-1_amd64.deb foundationdb-server_6.3.15-1_amd64.deb

// Node Exporter
wget https://github.com/prometheus/node_exporter/releases/download/v1.1.2/node_exporter-1.1.2.linux-amd64.tar.gz
sudo tar xf node_exporter-1.1.2.linux-amd64.tar.gz

cd node_exporter-1.1.2.linux-amd64
sudo ./node_exporter &

***
scrape_configs:
  # The job name is added as a label `job=<job_name>` to any timeseries scraped from this config.
  - job_name: 'prometheus'

    # metrics_path defaults to '/metrics'
    # scheme defaults to 'http'.

    static_configs:
            - targets: ['localhost:9090',172.31.29.109:9100]
***

fdbcli --exec "status json"