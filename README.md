# 🔄 SQL Server Database Mirroring - Guia Completo

[![SQL Server](https://img.shields.io/badge/SQL%20Server-2019%2B-blue)](https://www.microsoft.com/en-us/sql-server/)
[![License](https://img.shields.io/badge/License-MIT-green.svg)](LICENSE)
[![Documentation](https://img.shields.io/badge/Documentation-Complete-orange.svg)](docs/)

> Um guia completo e educativo sobre como implementar e gerenciar Database Mirroring no SQL Server.

## 📋 Índice

- [Sobre](#-sobre)
- [O que é Database Mirroring](#-o-que-é-database-mirroring)
- [Quando Usar](#-quando-usar)
- [Arquitetura](#-arquitetura)
- [Modos de Operação](#-modos-de-operação)
- [Pré-requisitos](#-pré-requisitos)
- [Instalação Rápida](#-instalação-rápida)
- [Documentação](#-documentação)
- [Scripts Prontos](#-scripts-prontos)
- [Contribuindo](#-contribuindo)
- [Licença](#-licença)

## 📖 Sobre

Este repositório fornece um tutorial completo e passo-a-passo sobre Database Mirroring no SQL Server. O objetivo é ajudar desenvolvedores e DBAs a entender, implementar e gerenciar soluções de alta disponibilidade usando Database Mirroring.

## 🎯 O que é Database Mirroring

**Database Mirroring** é uma solução de alta disponibilidade do SQL Server que mantém duas cópias de um banco de dados sincronizadas em diferentes servidores. Isso permite:

- ✅ **Alta Disponibilidade**: Failover automático em caso de falha
- ✅ **Recuperação de Desastres**: Cópia do banco de dados em local diferente
- ✅ **Proteção de Dados**: Dados são copiados em tempo real

### Arquitetura Básica

```
┌─────────────────┐                 ┌─────────────────┐
│   PRINCIPAL     │◄──────────────►│    MIRROR       │
│   (Principal)   │                 │   (Espelho)     │
│                 │                 │                 │
│  • Ativo        │                 │  • Inativo      │
│  • Aceita       │                 │  • Em sincronia │
│    conexões     │                 │  • Em standby   │
│  • Aplicação    │                 │                 │
└─────────────────┘                 └─────────────────┘
         │                                    │
         │                                    │
         └──────────────────┬────────────────┘
                            │
                    ┌───────▼───────┐
                    │  WITNESS      │
                    │  (Testemunha) │
                    │               │
                    │  • Opcional   │
                    │  • Facilita   │
                    │    failover   │
                    │    automático │
                    └───────────────┘
```

## 🔄 Quando Usar Database Mirroring

### ✅ Use Database Mirroring quando:

- Precisa de alta disponibilidade para um banco de dados crítico
- Quer failover rápido (segundos)
- Tem SQL Server Standard Edition
- Precisa de uma solução simples de HA
- Budget limitado para AlwaysOn Availability Groups

### ❌ Considere Alternativas quando:

- Tem múltiplos bancos de dados para proteger
- Precisa de leitura no servidor secundário
- Usa SQL Server Enterprise Edition
- Precisa de escala horizontal (read scaling)

> 💡 **Nota**: Para novas implementações, considere **AlwaysOn Availability Groups**, que é a solução moderna de HA do SQL Server.

## 🏗️ Arquitetura

### Componentes

| Componente | Descrição | Necessidade |
|------------|-----------|-------------|
| **Principal** | Servidor ativo que aceita conexões | ✅ Obrigatório |
| **Mirror** | Servidor secundário em standby | ✅ Obrigatório |
| **Witness** | Servidor testemunha para failover automático | ⚠️ Opcional |
| **Endpoint** | Porta TCP para comunicação entre servidores | ✅ Obrigatório |

### Modos de Operação

#### 1. High Safety (Síncrono)
- Dados são gravados no principal E no mirror
- **Failover automático** (com witness)
- Zero perda de dados
- Alta latência

#### 2. High Performance (Assíncrono)
- Dados são gravados apenas no principal
- **Sem failover automático**
- Possível perda de dados em caso de falha
- Baixa latência

## 📦 Pré-requisitos

### Requisitos do SQL Server
- ✅ SQL Server 2008 ou superior
- ✅ Recovery Model: **FULL**
- ✅ Backup completo inicial do banco de dados

### Requisitos de Rede
- ✅ Conectividade TCP entre servidores
- ✅ Porta específica para endpoint (default: 5022)
- ✅ Latência recomendada: < 1ms para síncrono

### Requisitos de Hardware
- ✅ CPU e memória similares em ambos os servidores
- ✅ Armazenamento adequado para logs

> 📖 **Pré-requisitos detalhados**: Veja [docs/01-pre-requisitos.md](docs/01-pre-requisitos.md)

## 🚀 Instalação Rápida

### Passo 1: Preparar o Banco de Dados

```sql
-- No servidor Principal
USE master;
GO

-- Verificar recovery model
SELECT name, recovery_model_desc
FROM sys.databases
WHERE name = 'SuaDatabase';
GO

-- Alterar para FULL se necessário
ALTER DATABASE SuaDatabase SET RECOVERY FULL;
GO

-- Fazer backup completo
BACKUP DATABASE SuaDatabase
TO DISK = 'C:\Backups\SuaDatabase_full.bak'
WITH FORMAT, COMPRESSION;
GO

-- Fazer backup do log
BACKUP LOG SuaDatabase
TO DISK = 'C:\Backups\SuaDatabase_log.trn'
WITH COMPRESSION;
GO
```

### Passo 2: Restaurar no Mirror

```sql
-- No servidor Mirror
RESTORE DATABASE SuaDatabase
FROM DISK = '\\Principal\Backups\SuaDatabase_full.bak'
WITH NORECOVERY, REPLACE;
GO

RESTORE LOG SuaDatabase
FROM DISK = '\\Principal\Backups\SuaDatabase_log.trn'
WITH NORECOVERY;
GO
```

### Passo 3: Configurar Endpoints

```sql
-- No servidor Principal
CREATE ENDPOINT Mirroring
STATE = STARTED
AS TCP (LISTENER_PORT = 5022)
FOR DATABASE_MIRRORING (ROLE = ALL);
GO

-- No servidor Mirror
CREATE ENDPOINT Mirroring
STATE = STARTED
AS TCP (LISTENER_PORT = 5022)
FOR DATABASE_MIRRORING (ROLE = ALL);
GO
```

### Passo 4: Configurar Mirroring

```sql
-- No servidor Mirror
ALTER DATABASE SuaDatabase
SET PARTNER = 'TCP://PrincipalServer:5022';
GO

-- No servidor Principal
ALTER DATABASE SuaDatabase
SET PARTNER = 'TCP://MirrorServer:5022';
GO

-- Opcional: Adicionar Witness (em ambos servidores)
ALTER DATABASE SuaDatabase
SET WITNESS = 'TCP://WitnessServer:5022';
GO
```

> 📚 **Tutorial completo**: Veja [docs/03-tutorial-passo-a-passo.md](docs/03-tutorial-passo-a-passo.md)

## 📚 Documentação

| Documento | Descrição |
|-----------|-----------|
| [01-pré-requisitos.md](docs/01-pre-requisitos.md) | Requisitos detalhados e preparação do ambiente |
| [02-conceitos-fundamentais.md](docs/02-conceitos-fundamentais.md) | Conceitos fundamentais e arquitetura |
| [03-tutorial-passo-a-passo.md](docs/03-tutorial-passo-a-passo.md) | Tutorial completo de configuração |
| [04-monitoramento.md](docs/04-monitoramento.md) | Como monitorar e manter o mirroring |
| [05-troubleshooting.md](docs/05-troubleshooting.md) | Resolução de problemas comuns |
| [06-melhores-praticas.md](docs/06-melhores-praticas.md) | Melhores práticas e recomendações |

## 💻 Scripts Prontos

Scripts SQL prontos para uso estão disponíveis na pasta `/scripts`:

| Script | Descrição |
|--------|-----------|
| [01-configurar-endpoint.sql](scripts/01-configurar-endpoint.sql) | Configurar endpoints de mirroring |
| [02-iniciar-mirroring.sql](scripts/02-iniciar-mirroring.sql) | Iniciar configuração do mirroring |
| [03-adicionar-witness.sql](scripts/03-adicionar-witness.sql) | Adicionar servidor testemunha |
| [04-monitoramento.sql](scripts/04-monitoramento.sql) | Scripts de monitoramento |
| [05-failover-manual.sql](scripts/05-failover-manual.sql) | Realizar failover manual |
| [06-remover-mirroring.sql](scripts/06-remover-mirroring.sql) | Remover configuração de mirroring |

```bash
# Exemplo de uso
sqlcmd -S PrincipalServer -U sa -P password -i scripts/01-configurar-endpoint.sql
sqlcmd -S MirrorServer -U sa -P password -i scripts/01-configurar-endpoint.sql
```

## 📊 Monitoramento

Monitorar o status do mirroring:

```sql
-- Verificar status
SELECT
    DB_NAME(database_id) AS DatabaseName,
    mirroring_state,
    mirroring_state_desc,
    mirroring_role,
    mirroring_role_desc,
    mirroring_safety_level,
    mirroring_safety_level_desc,
    mirroring_partner_name,
    mirroring_witness_name
FROM sys.database_mirroring
WHERE mirroring_guid IS NOT NULL;
GO
```

Saídas possíveis:

| Estado | Descrição |
|--------|-----------|
| 0 | Suspended |
| 1 | Disconnected |
| 2 | Synchronizing |
| 3 | Pending Failover |
| 4 | Synchronized |
| 5 | **Principal** (Ativo) |
| 6 | **Mirror** (Inativo) |

## 🔧 Troubleshooting

### Problema: Estado "Suspended"

```sql
-- Verificar erro no log
EXEC sp_readerrorlog;

-- Verificar conectividade
SELECT * FROM sys.dm_exec_connections;

-- Reiniciar mirroring
ALTER DATABASE SuaDatabase SET PARTNER RESUME;
```

### Problema: Falha no Failover Automático

```sql
-- Verificar se o witness está configurado
SELECT mirroring_witness_state, mirroring_witness_state_desc
FROM sys.database_mirroring;

-- Verificar conectividade com o witness
ALTER DATABASE SuaDatabase SET WITNESS = 'TCP://WitnessServer:5022';
```

> 🐛 **Solução de problemas completa**: Veja [docs/05-troubleshooting.md](docs/05-troubleshooting.md)

## ✨ Melhores Práticas

### 🎯 Recomendações

1. **Teste Regularmente**
   - Simule failovers regularmente
   - Teste o procedimento de recuperação

2. **Monitore Continuamente**
   - Configure alertas para eventos de falha
   - Monitore atraso de sincronização

3. **Documente Tudo**
   - Mantenha procedimentos atualizados
   - Documente as configurações

4. **Rede**
   - Use rede dedicada para o tráfego de mirroring
   - Configure Quality of Service (QoS)

5. **Backup**
   - Continue fazendo backups regulares
   - Teste restores dos backups

> 📖 **Melhores práticas completas**: Veja [docs/06-melhores-praticas.md](docs/06-melhores-praticas.md)

## 🤝 Contribuindo

Contribuições são bem-vindas! Por favor:

1. Fork o repositório
2. Crie uma branch para sua feature (`git checkout -b feature/MinhaFeature`)
3. Commit suas mudanças (`git commit -m 'Adiciona nova feature'`)
4. Push para a branch (`git push origin feature/MinhaFeature`)
5. Abra um Pull Request

## 📖 Recursos Adicionais

### Documentação Oficial

- [Database Mirroring (SQL Server) - Microsoft Learn](https://learn.microsoft.com/en-us/sql/database-engine/database-mirroring/database-mirroring-sql-server)
- [Setting Up Database Mirroring - Microsoft Learn](https://learn.microsoft.com/en-us/sql/database-engine/database-mirroring/setting-up-database-mirroring-sql-server)
- [Prerequisites, Restrictions, and Recommendations - Microsoft Learn](https://learn.microsoft.com/en-us/sql/database-engine/database-mirroring/prerequisites-restrictions-and-recommendations-for-database-mirroring)

### Vídeos Recomendados

- [SQL Server Database Mirroring Step by Step - YouTube](https://www.youtube.com/watch?v=rF0OmZt0ols)

### Tutoriais e Artigos

- [Configure SQL Server Database Mirroring with SSMS - MSSQLTips](https://www.mssqltips.com/sqlservertip/2464/configure-sql-server-database-mirroring-using-ssms)
- [How to Configure Database Mirroring for SQL Server - TatvaSoft](https://www.tatvasoft.com/blog/how-to-configure-database-mirroring-for-sql-server)
- [A Comprehensive Guide to Implementing Database Mirroring - Medium](https://medium.com/@rakesh.mr.0341/a-comprehensive-guide-to-implementing-database-mirroring-in-sql-server-9636a98dd0ac)

## 📝 Licença

Este projeto está licenciado sob a Licença MIT - veja o arquivo [LICENSE](LICENSE) para detalhes.

## 🙋‍♂️ Suporte

Encontrou um problema ou tem dúvidas?

1. Verifique a [documentação](docs/)
2. Procure no [troubleshooting](docs/05-troubleshooting.md)
3. Abra uma [issue](../../issues) no GitHub

---

⭐ **Se este repositório foi útil para você, considere dar uma estrela!**

Feito com ❤️ para a comunidade de SQL Server
