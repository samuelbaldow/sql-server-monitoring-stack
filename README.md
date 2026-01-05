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

    Recursos Principais (Key Features) ✨

Coleta de Métricas: Monitoramento detalhado de CPU, memória, disco, consultas SQL, locks, deadlocks e mais via integração oficial MSSQL-Zabbix.
Alertas Personalizáveis: Configuração de triggers para notificações via e-mail, Slack ou outros canais.
Dashboards Interativos: Visualização em tempo real com Grafana, incluindo gráficos de desempenho e histórico de dados.
Suporte a Múltiplos Bancos: Monitoramento de várias instâncias de bancos de dados no SQL Server.
Escalabilidade: Fácil expansão para monitorar múltiplos servidores Windows a partir de um único Ubuntu Server.

Requisitos (Requirements) 📋
Hardware e Software

Ubuntu Server:
Versão: 22.04 LTS ou superior.
Recursos mínimos: 4 GB RAM, 2 vCPUs, 50 GB de armazenamento.

Windows Server:
Versão: 2019 ou superior.
SQL Server: 2012 ou superior com múltiplos bancos de dados.

Conexão de Rede: Portas abertas para comunicação (ex: 10051 para Zabbix, 1433 para SQL Server ODBC).
Dependências:
Zabbix 6.0+ com Agent 2.
Grafana 9.0+.
MySQL 8.0+.
Driver ODBC para SQL Server no Windows.


Instalação (Installation) 🛠️
Passo 1: Configurar o Ubuntu Server

Atualizar o Sistema:textsudo apt update && sudo apt upgrade -y
Instalar Zabbix Server:
Adicione o repositório:textwget https://repo.zabbix.com/zabbix/6.0/ubuntu/pool/main/z/zabbix-release/zabbix-release_6.0-1+ubuntu22.04_all.deb
sudo dpkg -i zabbix-release_6.0-1+ubuntu22.04_all.deb
sudo apt update
Instale os pacotes:textsudo apt install zabbix-server-mysql zabbix-frontend-php zabbix-apache-conf zabbix-sql-scripts zabbix-agent -y

Instalar e Configurar MySQL:textsudo apt install mysql-server -y
sudo mysql_secure_installation
Crie o banco de dados:textsudo mysql -uroot -p
CREATE DATABASE zabbix CHARACTER SET utf8mb4 COLLATE utf8mb4_bin;
CREATE USER 'zabbix'@'localhost' IDENTIFIED BY 'sua_senha_forte';
GRANT ALL PRIVILEGES ON zabbix.* TO 'zabbix'@'localhost';
FLUSH PRIVILEGES;
EXIT;
Importe o schema:textzcat /usr/share/zabbix-sql-scripts/mysql/server.sql.gz | mysql -uzabbix -p zabbix

Configurar Zabbix Server:
Edite /etc/zabbix/zabbix_server.conf:textDBHost=localhost
DBName=zabbix
DBUser=zabbix
DBPassword=sua_senha_forte
Inicie os serviços:textsudo systemctl restart zabbix-server zabbix-agent apache2
sudo systemctl enable zabbix-server zabbix-agent apache2

Instalar Grafana:textsudo apt install -y software-properties-common
sudo add-apt-repository "deb https://packages.grafana.com/oss/deb stable main"
wget -q -O - https://packages.grafana.com/gpg.key | sudo apt-key add -
sudo apt update
sudo apt install grafana -y
sudo systemctl start grafana-server
sudo systemctl enable grafana-server
Instalar Integração MSSQL-Zabbix:
Baixe o template oficial do Zabbix Integrations.
Importe no frontend do Zabbix (acesso via http://seu_ip/zabbix).


Passo 2: Configurar o Windows Server

Instalar Zabbix Agent 2:
Baixe do site oficial.
Instale e edite C:\Program Files\Zabbix Agent 2\zabbix_agent2.conf:textServer=ip_do_ubuntu_server
ServerActive=ip_do_ubuntu_server:10051
Hostname=seu_hostname_windows
Inicie o serviço via Services.msc ou PowerShell:textRestart-Service "Zabbix Agent 2"

Configurar ODBC para SQL Server:
Abra ODBC Data Sources (64-bit).
Adicione uma conexão DSN para o SQL Server com credenciais apropriadas.


Passo 3: Integrar no Zabbix

No frontend do Zabbix, adicione o host Windows com o template MSSQL.
Configure itens de monitoramento para métricas específicas dos bancos de dados.

Configuração Avançada (Advanced Configuration) 🔧

Alertas: Crie ações no Zabbix para enviar e-mails ou integrar com ferramentas como Telegram/Slack.
Dashboards no Grafana: Instale o plugin Zabbix para Grafana e importe dashboards prontos.
Segurança: Use HTTPS para acessos, configure firewalls e autenticação forte.

Uso (Usage) 📊

Acesse o Zabbix: http://ip_ubuntu/zabbix (usuário: Admin, senha: zabbix).
Acesse o Grafana: http://ip_ubuntu:3000 (usuário: admin, senha: admin).
Monitore métricas em tempo real e configure alertas conforme necessário.

Contribuições (Contributions) 🤝
Contribuições são bem-vindas! Siga estes passos:

Fork o repositório.
Crie uma branch: git checkout -b feature/nova-funcionalidade.
Commit: git commit -m 'Adiciona nova funcionalidade'.
Push: git push origin feature/nova-funcionalidade.
Abra um Pull Request.

Licença (License) 📄
Este projeto está licenciado sob a MIT License - veja o arquivo LICENSE para detalhes.
