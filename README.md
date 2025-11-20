# 🚀 **Grafana + Prometheus Monitoring Project (Docker Based)**

A complete monitoring stack using **Prometheus, Grafana, and Node Exporter**.

---

# 📁 **Project Structure**

```
grafana-prometheus-project/
├── docker-compose.yml
├── prometheus/
│   ├── prometheus.yml
│   └── Dockerfile   (optional)
├── grafana/
│   ├── dashboards/
│   │   └── system_monitoring.json
│   └── provisioning/
│       ├── dashboards/dashboard.yml
│       ├── datasources/datasource.yml
└── node-exporter/
```

---

# 🐳 **1. docker-compose.yml**

```yaml
version: "3.8"

services:
  prometheus:
    image: prom/prometheus:latest
    container_name: prometheus
    volumes:
      - ./prometheus/prometheus.yml:/etc/prometheus/prometheus.yml
    ports:
      - "9090:9090"
    networks:
      - monitoring

  grafana:
    image: grafana/grafana:latest
    container_name: grafana
    depends_on:
      - prometheus
    ports:
      - "3000:3000"
    volumes:
      - ./grafana/provisioning:/etc/grafana/provisioning
      - ./grafana/dashboards:/var/lib/grafana/dashboards
    networks:
      - monitoring

  node-exporter:
    image: prom/node-exporter:latest
    container_name: node-exporter
    ports:
      - "9100:9100"
    networks:
      - monitoring

networks:
  monitoring:
```

---

# 📘 **2. Prometheus Configuration (prometheus/prometheus.yml)**

```yaml
global:
  scrape_interval: 5s

scrape_configs:
  - job_name: "prometheus"
    static_configs:
      - targets: ["prometheus:9090"]

  - job_name: "node_exporter"
    static_configs:
      - targets: ["node-exporter:9100"]
```

---

# 📊 **3. Grafana Auto-Provisioning**

### **grafana/provisioning/datasources/datasource.yml**

```yaml
apiVersion: 1

datasources:
  - name: Prometheus
    type: prometheus
    url: http://prometheus:9090
    access: proxy
    isDefault: true
```

---

### **grafana/provisioning/dashboards/dashboard.yml**

```yaml
apiVersion: 1

providers:
  - name: "Default"
    orgId: 1
    folder: ""
    type: file
    disableDeletion: false
    editable: true
    options:
      path: /var/lib/grafana/dashboards
```

---

# 📈 **4. Sample Dashboard (grafana/dashboards/system_monitoring.json)**

You can download any dashboard from Grafana Labs and place it here.
Example: *Node Exporter Full* dashboard → ID **1860**.

---

# ▶️ **5. How to Run the Project**

### **Step 1 — Clone Repo**

```bash
git clone https://github.com/YOUR_GITHUB/grafana-prometheus-project.git
cd grafana-prometheus-project
```

### **Step 2 — Start Stack**

```bash
docker-compose up -d
```

### **Step 3 — Access Services**

| Service       | URL                                            |
| ------------- | ---------------------------------------------- |
| Grafana       | [http://localhost:3000](http://localhost:3000) |
| Prometheus    | [http://localhost:9090](http://localhost:9090) |
| Node Exporter | [http://localhost:9100](http://localhost:9100) |

**Grafana Default Login:**

* Username: **admin**
* Password: **admin**
  (You will be prompted to change it)

---

# 📦 **6. Add More Exporters (Optional)**

### 🐳 cAdvisor (Docker Metrics)

Add this block in docker-compose.yml:

```yaml
cadvisor:
  image: gcr.io/cadvisor/cadvisor:latest
  ports:
    - "8080:8080"
  volumes:
    - /var/run/docker.sock:/var/run/docker.sock
    - /var/lib/docker/:/var/lib/docker
  networks:
    - monitoring
```

Update `prometheus.yml`:

```yaml
- job_name: "cadvisor"
  static_configs:
    - targets: ["cadvisor:8080"]
```

---

# 🖥️ **7. Add Alertmanager (Optional)**

### docker-compose.yml

```yaml
alertmanager:
  image: prom/alertmanager
  ports:
    - "9093:9093"
  volumes:
    - ./alertmanager:/etc/alertmanager
  networks:
    - monitoring
```
---
---

