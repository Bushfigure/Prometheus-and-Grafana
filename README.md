# Server Monitoring with Prometheus & Grafana

A production-grade monitoring solution using Prometheus for metrics collection and Grafana for visualization. I built this project to gain hands-on experience with system observability, alerting, and infrastructure monitoring — essential skills for DevOps and Site Reliability Engineering.

## 📊 Technologies
- **Prometheus** - Metrics collection and time-series database
- **Grafana** - Data visualization and alerting
- **Node Exporter** - System metrics exporter (CPU, memory, disk, network)
- **Alertmanager** - Alert handling and notification routing
- **ngrok** - Public URL tunneling for remote access
- **systemd** - Service management

## ✨ Features
Here's what you can do with this monitoring system:
- **Monitor System Metrics**: Track CPU usage, memory consumption, disk space, and network activity in real-time.
- **Visualize Data**: View beautiful, customizable dashboards in Grafana with system overview and detailed metrics.
- **Receive Alerts**: Get email notifications when CPU usage exceeds 80% or disk space drops below 15%.
- **Remote Access**: Access your dashboards from anywhere using ngrok tunneling.
- **Customize Monitoring**: Add more exporters for services like Nginx, MySQL, or Redis.
- **Historical Analysis**: Query and analyze historical metrics using PromQL.

## 🎨 Dashboards
- **System Overview Dashboard**: Shows CPU, memory, disk, and network graphs.
- **Node Exporter Full Dashboard (ID 1860)**: Comprehensive system monitoring with detailed metrics.
- **Custom Panels**: Build your own panels using PromQL queries.

## 🔧 Keyboard Shortcuts (PromQL Quick Reference)
Speed up your monitoring queries:
- **CPU Usage**: `100 - (avg by (instance) (rate(node_cpu_seconds_total{mode="idle"}[5m])) * 100)`
- **Memory Usage**: `(1 - (node_memory_MemAvailable_bytes / node_memory_MemTotal_bytes)) * 100`
- **Disk Usage**: `100 - ((node_filesystem_avail_bytes{mountpoint="/"} * 100) / node_filesystem_size_bytes{mountpoint="/"})`
- **Network Traffic**: `rate(node_network_receive_bytes_total[5m])`

## 🚀 The Process

I started by installing Prometheus as the core metrics collection engine. This involved creating a dedicated system user, setting up directories, and configuring it as a systemd service for automatic startup.

Next, I installed Node Exporter to collect system-level metrics like CPU, memory, disk, and network statistics. I configured Prometheus to scrape these metrics every 15 seconds and verified that everything was working through the targets page.

Then came Grafana — the visualization layer. I installed Grafana, connected it to Prometheus as a data source, and imported a pre-built dashboard (Node Exporter Full - ID 1860) to immediately visualize system metrics. I also created custom panels to understand PromQL queries better.

To make the monitoring system proactive, I implemented alerting. I created alert rules in Prometheus for high CPU usage and low disk space. I configured SMTP in Grafana to send email notifications. This was tricky because I had to generate a Google App Password and properly format the grafana.ini configuration.

Finally, I used ngrok to expose Grafana publicly so I could access it from anywhere. This required registering for an ngrok account and configuring the authentication token.

Throughout the process, I documented errors and their solutions — turning problems into learning opportunities.

## 💡 What I Learned

During this project, I gained practical skills in monitoring and observability that go beyond just installing tools.

### **Prometheus Configuration and YAML Syntax:**
- **Attention to Detail**: YAML is strict. One wrong indentation can break everything. I learned to be meticulous with spacing and structure after encountering several parsing errors.
- **Scraping Intervals**: Understanding how to balance data resolution with storage requirements by adjusting scrape intervals.

### **Systemd Service Management:**
- **Service Creation**: I learned how to create, enable, and manage systemd services for Prometheus, Node Exporter, and Grafana.
- **Troubleshooting Service Failures**: Using `systemctl status` and journal logs to debug service start failures.

### **Alerting and Notification Configuration:**
- **SMTP Configuration**: Setting up email notifications in Grafana required understanding SMTP servers, authentication, and App Passwords for Gmail.
- **Alert Rules**: Writing PromQL expressions for alert conditions and understanding the `for` clause for preventing false positives.

### **Networking and Access:**
- **Port Binding**: Understanding the difference between binding to `127.0.0.1` vs `0.0.0.0` and how it affects accessibility.
- **Ngrok Tunneling**: Using ngrok to expose local services to the internet — a valuable skill for sharing work or testing in production-like environments.

### **Troubleshooting Mindset:**
- **Error Analysis**: Each error taught me something new. I learned to read error messages carefully, check logs, and systematically isolate problems.
- **Documentation**: Documenting every step — including the failures — helped me solidify my understanding and create a useful reference for the future.

### **Observability Concepts:**
- **Metrics vs Logs**: Understanding the difference between monitoring metrics (numbers) and logs (events) and how they complement each other.
- **Service Discovery**: Learning how Prometheus discovers and scrapes targets.

## 🔄 How Can It Be Improved?

- **Add More Exporters**: Integrate exporters for Nginx, MySQL, MongoDB, or Redis to monitor application-level services.
- **Implement Recording Rules**: Pre-compute expensive queries to improve dashboard loading performance.
- **Add Loki Integration**: Bring in log aggregation to correlate metrics with logs in a single view.
- **Multi-Server Monitoring**: Deploy Node Exporter on multiple servers and configure Prometheus to scrape all of them.
- **Advanced Alerting**: Set up Alertmanager with Slack integration, grouping, and inhibition rules.
- **Service Discovery**: Use file-based or cloud provider service discovery for dynamic environments.
- **Custom Application Metrics**: Instrument custom applications with Prometheus client libraries.
- **CI/CD Integration**: Automate the deployment of monitoring stack using Ansible or Terraform.

## 🚦 Running the Project

To set up the monitoring system in your local environment, follow these steps:

1. Clone this repository:
   ```bash
   git clone https://github.com/Bushfigure/Prometheus-and-Grafana.git
   cd Prometheus-and-Grafana
   ```

2. Install Prometheus:
   ```bash
   sudo useradd --no-create-home --shell /bin/false prometheus
   sudo mkdir /etc/prometheus /var/lib/prometheus
   cd /tmp
   wget https://github.com/prometheus/prometheus/releases/download/v2.53.4/prometheus-2.53.4.linux-amd64.tar.gz
   tar xvf prometheus-2.53.4.linux-amd64.tar.gz
   sudo cp prometheus-2.53.4.linux-amd64/{prometheus,promtool} /usr/local/bin/
   sudo cp -r prometheus-2.53.4.linux-amd64/{consoles,console_libraries,prometheus.yml} /etc/prometheus/
   sudo cp systemd/prometheus.service /etc/systemd/system/
   sudo systemctl daemon-reload
   sudo systemctl enable --now prometheus
   ```

3. Install Node Exporter:
   ```bash
   cd /tmp
   wget https://github.com/prometheus/node_exporter/releases/download/v1.8.2/node_exporter-1.8.2.linux-amd64.tar.gz
   tar xvf node_exporter-1.8.2.linux-amd64.tar.gz
   sudo cp node_exporter-1.8.2.linux-amd64/node_exporter /usr/local/bin/
   sudo useradd --no-create-home --shell /bin/false nodeusr
   sudo cp systemd/node_exporter.service /etc/systemd/system/
   sudo systemctl daemon-reload
   sudo systemctl enable --now node_exporter
   ```

4. Install Grafana:
   ```bash
   wget -q -O - https://apt.grafana.com/gpg.key | sudo gpg --dearmor -o /usr/share/keyrings/grafana.gpg
   echo "deb [signed-by=/usr/share/keyrings/grafana.gpg] https://apt.grafana.com stable main" | sudo tee /etc/apt/sources.list.d/grafana.list
   sudo apt update
   sudo apt install grafana
   sudo systemctl enable --now grafana-server
   ```

5. Access Grafana at `http://localhost:3000` (default login: `admin`/`admin`).

6. Add Prometheus data source: `http://localhost:9090`.

7. Import dashboard ID `1860` for Node Exporter Full.

8. Configure alert rules by copying `prometheus/alerts.yml` to `/etc/prometheus/`.

9. Configure email notifications using `grafana/grafana.ini.example` as a guide.

10. (Optional) Expose with ngrok:
    ```bash
    ngrok config add-authtoken <YOUR_TOKEN>
    ngrok http 3000
    ```

## 🐛 Common Errors & Solutions

### Error 1: `Failed to enable unit: Unit file prometheus.service does not exist`

**Cause:** The systemd service file was not created.

**Solution:** Create `/etc/systemd/system/prometheus.service` with the correct configuration.

### Error 2: `field static_configs already set in type config.ScrapeConfig`

**Cause:** Duplicate `static_configs` field in `prometheus.yml`.

**Solution:** Ensure each job has only one `static_configs` block with proper indentation.

### Error 3: `parsing YAML file: yaml: unmarshal errors`

**Cause:** Incorrect YAML indentation.

**Solution:** Use 2 spaces per level, no tabs. YAML is strict about indentation.

### Error 4: `404 Not Found - Error returned querying the Prometheus API`

**Cause:** Incorrect Prometheus URL in Grafana.

**Solution:** Use exactly `http://localhost:9090` (no trailing slash).

### Error 5: `SMTP not configured, check your grafana.ini config file's [smtp] section`

**Cause:** SMTP settings missing in `grafana.ini`.

**Solution:** Uncomment and configure the `[smtp]` section with valid email credentials.

### Error 6: `ngrok config add-authtoken: Unrecognized command: config`

**Cause:** Using older ngrok v2 syntax.

**Solution:** Use `ngrok authtoken <TOKEN>` for v2, or update to v3.

### Error 7: `ERR_NGROK_4018 - This ngrok session is not authenticated`

**Cause:** No authtoken configured.

**Solution:** Run `ngrok config add-authtoken <YOUR_TOKEN>` (v3) or `ngrok authtoken <YOUR_TOKEN>` (v2).

## 📁 Repository Structure

```
Prometheus-and-Grafana/
├── README.md
├── .gitignore
├── prometheus/
│   ├── prometheus.yml
│   └── alerts.yml
├── grafana/
│   └── grafana.ini.example
├── systemd/
│   ├── prometheus.service
│   └── node_exporter.service
└── screenshots/
    ├── dashboard.png
    ├── targets.png
    └── alert-email.png
```

## 📚 References

- [Prometheus Documentation](https://prometheus.io/docs/)
- [Grafana Documentation](https://grafana.com/docs/)
- [Node Exporter](https://github.com/prometheus/node_exporter)
- [PromQL Querying](https://prometheus.io/docs/prometheus/latest/querying/basics/)
- [Alertmanager](https://prometheus.io/docs/alerting/latest/alertmanager/)
- [ngrok Documentation](https://ngrok.com/docs)

## 👤 Author

**Samuel Olumide Aransiola**
- GitHub: [@Bushfigure](https://github.com/Bushfigure)

## 📄 License

This project is open source and available under the MIT License.

---

⭐️ If you found this project helpful, please give it a star on GitHub!
