# SQL Server Monitoring Stack
[![MIT License](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)
[![SQL Server](https://img.shields.io/badge/SQL%20Server-2012%2B-red.svg)](https://www.microsoft.com/en-us/sql-server)
[![Zabbix](https://img.shields.io/badge/Zabbix-6.0%2B-orange.svg)](https://www.zabbix.com/)
[![Grafana](https://img.shields.io/badge/Grafana-9.0%2B-purple.svg)](https://grafana.com/)
[![Ubuntu](https://img.shields.io/badge/Ubuntu-22.04%2B-green.svg)](https://ubuntu.com/)
[![Windows Server](https://img.shields.io/badge/Windows%20Server-2019%2B-blue.svg)](https://www.microsoft.com/en-us/windows-server)
### Complete monitoring solution for SQL Server with Zabbix metrics collection and Grafana visualization
## Visão Geral (Overview) 🚀
O SQL Server Monitoring Stack é uma solução completa e integrada para monitoramento de instâncias do SQL Server. Utilizando Zabbix para coleta de métricas em tempo real, MySQL para armazenamento persistente e Grafana para visualização intuitiva, este projeto oferece monitoramento proativo com alertas personalizáveis e dashboards interativos. Ideal para administradores de banco de dados que precisam de insights rápidos sobre desempenho, saúde e disponibilidade.
## Arquitetura (Architecture) 🏗️
A arquitetura é dividida entre servidores Windows e Ubuntu, garantindo coleta eficiente de métricas e visualização centralizada. Abaixo está um diagrama representando os componentes e fluxos:
```mermaid
---
config:
  layout: dagre
---
flowchart TB
 subgraph subGraph0["Windows Server"]
        SQL["SQL Server"]
        ZA["Zabbix Agent2"]
        n1["Database"]
        n2["Database"]
  end
 subgraph subGraph1["Ubuntu Server"]
        ZS["Zabbix Server"]
        DB[("MySQL Database")]
        GF["Grafana"]
  end
 subgraph s1["Results"]
        n3["Alerts"]
        n4["Dashboard"]
        n5["Team"]
  end
    SQL <-- ODBC Connection --> ZA
    ZS -- Store Metrics --> DB
    GF -- Query Data --> ZS
    ZA <-. Push Metrics .-> ZS
    Admin["Monitoring-Stack"] -- <br> --> GF
    SQL --- n1 & n2
    Admin --- s1
    n3 -.-> n5
    n4 --> n5
    n1@{ shape: db}
    n2@{ shape: db}
    n3@{ shape: rect}
    n4@{ shape: rect}
    n5@{ shape: rect}
