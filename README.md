# 🔍 SQL Server Comprehensive Monitoring Solution

<div align="center">

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
[![SQL Server](https://img.shields.io/badge/SQL%20Server-2012+-CC2927?logo=microsoftsqlserver)](https://www.microsoft.com/sql-server)
[![Zabbix](https://img.shields.io/badge/Zabbix-5.0+-D40000?logo=zabbix)](https://www.zabbix.com/)
[![Grafana](https://img.shields.io/badge/Grafana-8.0+-F46800?logo=grafana)](https://grafana.com/)
[![Ubuntu](https://img.shields.io/badge/Ubuntu-20.04+-E95420?logo=ubuntu)](https://ubuntu.com/)
[![Windows Server](https://img.shields.io/badge/Windows%20Server-2016+-0078D6?logo=windows)](https://www.microsoft.com/windows-server)

**Enterprise-grade monitoring solution for SQL Server environments with real-time dashboards, proactive alerts, and comprehensive performance tracking.**

</div>

## 📊 Live Dashboard Preview

<div align="center">
  <img src="https://via.placeholder.com/900x450.png/0078D6/ffffff?text=SQL+Server+Performance+Dashboard" alt="Dashboard Preview" width="800"/>
  <br/>
  <em>Real-time monitoring dashboard showing SQL Server performance metrics</em>
</div>

## 🎯 Features at a Glance

| Feature | Description | Benefits |
|---------|-------------|----------|
| **📈 Real-time Monitoring** | Live metrics collection every 30 seconds | Immediate visibility into performance issues |
| **🚨 Proactive Alerting** | Custom triggers for critical conditions | Prevent downtime before it happens |
| **📊 Historical Analysis** | Data retention up to 1 year | Trend analysis and capacity planning |
| **🔍 Query Performance** | Top expensive queries tracking | Identify and optimize slow-running queries |
| **💾 Backup Monitoring** | Automated backup success/failure tracking | Ensure data recoverability |
| **📱 Multi-platform** | Ubuntu + Windows integration | Covers heterogeneous environments |
| **🐳 Docker Ready** | Containerized deployment options | Quick setup and scalability |

## 🏗️ System Architecture

```mermaid
graph TB
    subgraph "Windows Server"
        SQL[SQL Server 2022]
        ZA[Zabbix Agent 6.0]
        
        SQL -- "Performance Counters<br>SQL Queries" --> ZA
        ZA -- "Encrypted Connection<br>TCP 10051" --> ZS
    end
    
    subgraph "Ubuntu Server 22.04"
        ZS[Zabbix Server]
        DB[(MySQL 8.0<br>Metrics Storage)]
        GF[Grafana 9.0<br>Dashboards]
        NG[Nginx<br>Reverse Proxy]
        
        ZS -- "Store Metrics" --> DB
        GF -- "Query Data" --> ZS
        NG -- "SSL Termination" --> GF
    end
    
    Admin[Administrator] -- "Access Dashboards<br>HTTPS 443" --> NG
    Alert[Alert System] -- "Email/SMS/Teams" --> ZS
