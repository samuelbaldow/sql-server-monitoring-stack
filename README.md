# 📊 Monitoramento SQL Server: Zabbix + Grafana + Ubuntu

![Zabbix](https://img.shields.io/badge/Zabbix-6.0%2B-red?style=for-the-badge&logo=zabbix&logoColor=white)
![Grafana](https://img.shields.io/badge/Grafana-9.0%2B-orange?style=for-the-badge&logo=grafana&logoColor=white)
![SQL Server](https://img.shields.io/badge/SQL%20Server-2019%2B-red?style=for-the-badge&logo=microsoft-sql-server&logoColor=white)
![Ubuntu](https://img.shields.io/badge/Ubuntu-22.04-E94333?style=for-the-badge&logo=ubuntu&logoColor=white)

Este projeto apresenta uma solução completa de observabilidade para ambientes **Microsoft SQL Server**. Através da integração entre Zabbix e Grafana, é possível monitorar a saúde do sistema operacional Windows e a performance profunda das instâncias de banco de dados.

---

## 🏗️ Arquitetura do Projeto

A solução é distribuída para garantir alta performance e isolamento de recursos entre o coletor e o banco monitorado.

```mermaid
flowchart TB
    subgraph Windows_Server["Windows Server (Target)"]
        SQL["SQL Server"]
        ZA["Zabbix Agent 2"]
        DB1[("Database 01")]
        DB2[("Database 02")]
        
        SQL --- DB1
        SQL --- DB2
        ZA -- "Query (ODBC/Native)" --> SQL
    end

    subgraph Ubuntu_Server["Ubuntu Server (Monitoring Stack)"]
        ZS["Zabbix Server"]
        DB_ZBX[("MySQL Database")]
        GF["Grafana"]
        
        ZS -- "Store Metrics" --> DB_ZBX
        GF -- "Query Data" --> ZS
    end

    subgraph Output["Results"]
        AL["Alerts (Trigger)"]
        DS["Dashboards"]
        TM["Team/Admin"]
        
        AL --> TM
        DS --> TM
    end

    ZA -- "Push/Pull Metrics" --> ZS
    ZS --> AL
    GF --> DS
