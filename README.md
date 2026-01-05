# 📊 SQL Server Monitoring with Zabbix & Grafana

SQL Server monitoring project using **Zabbix** for metrics collection and alerting, and **Grafana** for real-time visualization.  
The architecture is designed to provide **observability, performance monitoring, and proactive incident detection** for SQL Server environments.

---

## 🏗️ Architecture Overview

### Ubuntu Server
- Zabbix Server
- Official MSSQL Zabbix integration
- MySQL database for metrics storage
- Grafana for dashboards and visualization

### Windows Server
- SQL Server (monitored instance)

Zabbix collects metrics from SQL Server using its official MSSQL integration and stores the data in MySQL. Grafana consumes Zabbix data to display real-time dashboards.

---

## 🔄 Monitoring Flow

1. SQL Server runs on Windows Server
2. Zabbix Server (Ubuntu) collects SQL Server metrics
3. Metrics are stored in MySQL (Zabbix backend)
4. Grafana queries Zabbix to render dashboards
5. Zabbix triggers alerts when thresholds are exceeded

---

## 📈 Metrics Collected

- SQL Server availability and uptime
- CPU, memory, and disk usage
- Active sessions and connections
- Database file size and free space
- Performance indicators and latency
- Backup status and execution history
- SQL Agent job status

---

## 🚨 Alerting

Zabbix is responsible for alerting based on predefined triggers, allowing:
- Early detection of performance degradation
- Proactive incident response
- Improved availability and reliability

Alerts can be delivered via email, webhooks, or integrated notification systems.

---

## 🧰 Technologies Used

- Microsoft SQL Server
- Windows Server
- Ubuntu Server
- Zabbix
- Grafana
- MySQL

---

## ⚙️ SQL Server Monitoring Account (Example)

```sql
USE master;
CREATE LOGIN zabbix_monitor WITH PASSWORD = 'Strong@Password!';
CREATE USER zabbix_monitor FOR LOGIN zabbix_monitor;
GRANT VIEW SERVER STATE TO zabbix_monitor;
GRANT VIEW ANY DEFINITION TO zabbix_monitor;
