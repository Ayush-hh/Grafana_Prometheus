<div align="center">
    <img src="https://github.com/Anugrah2334/Grafana-Prometheus/blob/main/assests/Grafana.png" alt="Grafana Logo" style="width: 200px; height: auto;">
    <img src="https://github.com/Anugrah2334/Grafana-Prometheus/blob/main/assests/Prometheus.png" alt="Prometheus Logo" style="width: 200px; height: auto;">
</div>

# Monitoring Stack with Prometheus and Grafana

Created by Ayush Sharma | SAP- 500106907

This project sets up a complete monitoring stack using Prometheus and Grafana with Node Exporter for system metrics collection.

## Architecture

-   **Prometheus**: Time-series database for metrics collection
-   **Grafana**: Visualization platform for metrics
-   **Node Exporter**: Collects system metrics from the host machine

## Prerequisites

-   Docker
-   Docker Compose

## Quick Start

1. Clone this repository
2. Run the monitoring stack:

```bash
docker-compose up -d
```

![Image](https://github.com/Anugrah2334/Grafana-Prometheus/blob/main/assests/Screenshot1.png)

## Accessing the Services

-   **Prometheus**: http://localhost:9090

![Image](https://github.com/Anugrah2334/Grafana-Prometheus/blob/main/assests/Screenshot2.png)

-   **Grafana**: http://localhost:3000
    -   Default credentials: admin/admin

![Image](https://github.com/Anugrah2334/Grafana-Prometheus/blob/main/assests/Screenshot3.png)

-   **Node Exporter Metrics**: http://localhost:9100/metrics

![Image](https://github.com/Anugrah2334/Grafana-Prometheus/blob/main/assests/Screenshot4.png)

## Verify Node Exporter and Grafana UP in Prometheus

![Image](https://github.com/Anugrah2334/Grafana-Prometheus/blob/main/assests/Screenshot5.png)

## Setting Up Grafana Dashboard

1. Log in to Grafana (http://localhost:3000)

![Image](https://github.com/Anugrah2334/Grafana-Prometheus/blob/main/assests/Screenshot5.png)

2. Add Prometheus as a data source:
    - Go to Configuration → Data Sources
    - Click "Add data source"
    - Select Prometheus
    - URL: http://prometheus:9090
    - Click "Save & Test"

![Image](https://github.com/Anugrah2334/Grafana-Prometheus/blob/main/assests/Screenshot6.png)

![Image](https://github.com/Anugrah2334/Grafana-Prometheus/blob/main/assests/Screenshot7.png)

3. Import the Node Exporter dashboard:
    - Click "+" → Import
    - Enter dashboard ID: 1860
    - Select Prometheus data source
    - Click Import

![Image](https://github.com/Anugrah2334/Grafana-Prometheus/blob/main/assests/Screenshot8.png)

![Image](https://github.com/Anugrah2334/Grafana-Prometheus/blob/main/assests/Screenshot9.png)

## Dashboard

![Image](https://github.com/Anugrah2334/Grafana-Prometheus/blob/main/assests/Screenshot10.png)

## Configuration Files

### docker-compose.yml

```yaml
version: "3"
services:
    prometheus:
        image: prom/prometheus
        ports:
            - "9090:9090"
        volumes:
            - ./prometheus.yml:/etc/prometheus/prometheus.yml
        networks:
            - monitoring
    grafana:
        image: grafana/grafana
        ports:
            - "3000:3000"
        networks:
            - monitoring
        depends_on:
            - prometheus
    node-exporter:
        image: prom/node-exporter:v1.9.1
        ports:
            - "9100:9100"
        networks:
            - monitoring
        volumes:
            - /proc:/host/proc:ro
            - /sys:/host/sys:ro
            - /:/rootfs:ro
        command:
            - "--path.procfs=/host/proc"
            - "--path.sysfs=/host/sys"
            - "--collector.filesystem.mount-points-exclude=^/(sys|proc|dev|host|etc)($$|/)"

networks:
    monitoring:
        driver: bridge
```

### prometheus.yml

```yaml
global:
    scrape_interval: 15s
    evaluation_interval: 15s

scrape_configs:
    - job_name: "prometheus"
      static_configs:
          - targets: ["localhost:9090"]

    - job_name: "node-exporter"
      static_configs:
          - targets: ["node-exporter:9100"]
```

## Troubleshooting

### Common Issues

1. **Node Exporter shows as DOWN in Prometheus**

    - Check if Node Exporter is running: http://localhost:9100/metrics
    - Verify the target configuration in prometheus.yml
    - Check Node Exporter logs:

    ```bash
    docker-compose logs node-exporter
    ```

2. **No data in Grafana**

    - Ensure Prometheus is scraping Node Exporter
    - Check if the correct data source is selected in Grafana
    - Verify the time range in Grafana dashboard
    - Check Prometheus logs:

    ```bash
    docker-compose logs prometheus
    ```

3. **Service not starting**
    - Check Docker logs: `docker-compose logs [service_name]`
    - Ensure ports 9090, 3000, and 9100 are not in use
    - Check if Docker is running:
    ```bash
    docker ps
    ```

## Maintenance

-   To stop all services:

```bash
docker-compose down
```

-   To restart services:

```bash
docker-compose restart
```

-   To view logs of a specific service:

```bash
docker-compose logs -f [service_name]
```


