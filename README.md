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
Run Docker Compose to start your services:
```
docker-compose up -d
```
# Access the UIs:
```

```
Grafana: http://localhost:3000
Prometheus: http://localhost:9090
cAdvisor: http://localhost:8080
Set up Grafana:

After logging in to Grafana, add Prometheus as a data source by navigating to Configuration > Data Sources > Add data source, and select Prometheus.
Enter the URL for Prometheus, which would be http://prometheus:9090 in this setup.
You can now create dashboards in Grafana to visualize metrics collected by Prometheus.
This Docker Compose setup will start the necessary services for monitoring with Grafana and Prometheus, along with Node Exporter and cAdvisor for gathering system and container metrics, and Watchtower for automatically updating running containers. Remember to customize the Prometheus and Grafana configurations according to your specific needs and security practices.





