# Instructions:

Create a directory for your project and navigate into it.
Save this YAML configuration as docker-compose.yml in your project directory.
Create a Prometheus configuration file. Inside the prometheus directory (you'll need to create this directory where your docker-compose.yml file is located), create a file named prometheus.yml with the following configuration to scrape metrics from Prometheus itself, Node Exporter, and cAdvisor:
```
global:
  scrape_interval: 15s

scrape_configs:
  - job_name: 'prometheus'
    static_configs:
      - targets: ['localhost:9090']

  - job_name: 'nodeexporter'
    static_configs:
      - targets: ['nodeexporter:9100']

  - job_name: 'cadvisor'
    static_configs:
      - targets: ['cadvisor:8080']
```
promtail config.yaml /etc/promtail/config.yaml
```
server:
  http_listen_port: 9080
  grpc_listen_port: 0

positions:
  filename: /tmp/positions.yaml

clients:
  - url: http://loki:3100/loki/api/v1/push

scrape_configs:
  - job_name: system
    static_configs:
      - targets:
          - localhost
        labels:
          job: varlogs
          __path__: /var/log/*log

  - job_name: journal
    journal:
      max_age: 12h
      labels:
        job: systemd-journal
    relabel_configs:
      - source_labels: ['__journal__systemd_unit']
        target_label: 'unit'

  - job_name: docker_containers
    static_configs:
      - targets:
          - localhost
        labels:
          job: docker_logs
          __path__: /var/lib/docker/containers/*/*.log
```
loki local-config.yaml /etc/loki/local-config.yaml
```
auth_enabled: false

server:
  http_listen_port: 3100

ingester:
  lifecycler:
    address: 127.0.0.1
    ring:
      kvstore:
        store: inmemory
      replication_factor: 1
  chunk_idle_period: 1h
  chunk_retain_period: 24h
  max_transfer_retries: 3

schema_config:
  configs:
  - from: 2020-10-24
    store: boltdb-shipper
    object_store: filesystem
    schema: v11
    index:
      prefix: index_
      period: 24h

storage_config:
  boltdb_shipper:
    active_index_directory: /var/lib/loki/index
    cache_location: /var/lib/loki/index_cache
    shared_store: filesystem
  filesystem:
    directory: /var/lib/loki/chunks

limits_config:
  enforce_metric_name: true
  reject_old_samples: true
  reject_old_samples_max_age: 720h

chunk_store_config:
  max_look_back_period: 24h

table_manager:
  retention_deletes_enabled: true
  retention_period: 720h

ingestion_rate_limiter:
  enabled: true
  burst_size_bytes: 4194304 # 4MB/s, adjust as necessary
  ingestion_rate_bytes: 4194304 # 4MB/s, adjust as necessary
```
Run Docker Compose to start your services:
```
docker-compose up -d
```
# Access the UIs:

Grafana: http://localhost:3000

Prometheus: http://localhost:9090

cAdvisor: http://localhost:8080

Set up Grafana:

After logging in to Grafana, add Prometheus as a data source by navigating to Configuration > Data Sources > Add data source, and select Prometheus.
Enter the URL for Prometheus, which would be http://prometheus:9090 in this setup.
You can now create dashboards in Grafana to visualize metrics collected by Prometheus.
This Docker Compose setup will start the necessary services for monitoring with Grafana and Prometheus, along with Node Exporter and cAdvisor for gathering system and container metrics, and Watchtower for automatically updating running containers. Remember to customize the Prometheus and Grafana configurations according to your specific needs and security practices.






