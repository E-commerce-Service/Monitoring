# 📊 Observability & Monitoring Stack

![Prometheus](https://img.shields.io/badge/Prometheus-v3.6-E6522C?style=for-the-badge&logo=prometheus&logoColor=white)
![Grafana](https://img.shields.io/badge/Grafana-v12.3-F46800?style=for-the-badge&logo=grafana&logoColor=white)
![Docker](https://img.shields.io/badge/Docker-2496ED?style=for-the-badge&logo=docker&logoColor=white)
![IaC](https://img.shields.io/badge/IaC-Provisioning-blueviolet?style=for-the-badge)

This repository contains the centralized monitoring infrastructure for the E-commerce Microservices ecosystem. It utilizes **Prometheus** for metrics scraping and **Grafana** for visualization, using a fully declarative approach for datasources and dashboards.

## 🏗️ Architecture

The stack runs on the shared Docker network to discover and scrape metrics from the application services and databases.

* **Prometheus (TSDB)**: Scrapes metrics from Spring Boot Actuator endpoints, PostgreSQL exporters, MongoDB exporters, and RabbitMQ.
* **Grafana (UI)**: Visualizes the data. It is pre-configured to load dashboards from the file system.

## 📂 Project Structure

```text
.
├── docker-compose.yml       # Service orchestration
├── prometheus.yml           # Scrape configs (Targets: Apps, DBs, Broker)
└── grafana/
    └── provisioning/        # Grafana IaC Configuration
        ├── datasources/     # Auto-connects to Prometheus
        └── dashboards/      # Auto-loads JSON dashboards
            ├── json/        # Raw Dashboard definitions
            └── all.yml      # Provider configuration
```

## 🚀 Key Features: Dashboard as Code

This project uses **Grafana Provisioning** to persist dashboards. You do not need to manually import JSON files via the UI. If the container is destroyed and recreated, all dashboards remain available.

### 📈 Included Dashboards

| Dashboard Name | File | Description |
| :--- | :--- | :--- |
| **Spring Boot System** | `Spring-Boot-2-1-System-Monitor...json` | CPU, Memory, Uptime, and Heap usage for Java Apps. |
| **JVM Micrometer** | `JVM-(Micrometer)...json` | Detailed JVM metrics (Garbage Collection, Threads, Buffers). |
| **PostgreSQL** | `PostgreSQL-Database...json` | Active connections, transactions, and cache hit rates. |
| **MongoDB** | `Mongodb-Dashboard...json` | Document operations, connections, and memory usage. |
| **RabbitMQ** | `RabbitMQ-Overview...json` | Queue depth, message rates, and consumer count. |

## 🛠️ Configuration Details

### Datasource Provisioning (`datasource.yml`)
Grafana is configured to automatically add **Prometheus** as the default datasource on startup:
```yaml
datasources:
  - name: Prometheus
    url: http://prometheus:9090
    isDefault: true
```

### Dashboard Provisioning (`all.yml`)
Grafana watches the `/var/lib/grafana/dashboards` directory inside the container. Any JSON file placed in `grafana/provisioning/dashboards/json` on your host is mapped there and instantly loaded.

## ⚡ Quick Start

1.  **Start the Stack:**
    ```bash
    docker-compose up -d
    ```

2.  **Access the Interfaces:**
    * **Grafana**: `http://localhost:3000`
        * *Default Login*: `admin` / `admin` (You will be prompted to change this on first login).
    * **Prometheus**: `http://localhost:9090`

3.  **Verify Data:**
    Log in to Grafana, go to **Dashboards**, and you will see the "My Dashboards" folder containing the pre-loaded views.

## 🌐 Network

This stack attaches to the external network `shared_ecommerce_network` to communicate with:
* `ecommerce-app` (Product API)
* `reviews-app` (Reviews API)
* `payment-app` (Payment API)
* `postgres-exporter`
* `mongodb-exporter`
* `rabbitmq`

## 📄 License

Configured for internal monitoring of the E-commerce project.
