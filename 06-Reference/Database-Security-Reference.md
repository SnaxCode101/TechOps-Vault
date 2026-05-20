---
title: Database Security Reference
tags:
  - reference
  - database
  - security
  - sql
  - mysql
  - postgresql
  - mssql
topic: Reference
difficulty: intermediate
created: 2026-05-16
updated: 2026-05-16
source: OWASP, CIS Benchmarks, Microsoft SQL Server docs
---

# Database Security Reference

## Overview
Databases are the most common target in breaches — they hold credentials, PII, financial records, and application secrets. This reference covers hardening, common attack vectors, and security controls across the most common database engines.

---

## Common Database Ports

| Database | Default Port |
|----------|-------------|
| MySQL / MariaDB | 3306 |
| PostgreSQL | 5432 |
| Microsoft SQL Server | 1433 |
| Oracle | 1521 |
| MongoDB | 27017 |
| Redis | 6379 |
| SQLite | (file-based, no port) |

---

## MySQL / MariaDB

### Hardening Checklist
```sql
-- Run mysql_secure_installation after install
-- Removes: anonymous users, test DB, root remote login

-- Check who can login from where
SELECT user, host, authentication_string FROM mysql.user;

-- Remove anonymous users
DELETE FROM mysql.user WHERE User='';

-- Disable remote root login
DELETE FROM mysql.user WHERE User='root' AND Host NOT IN ('localhost', '127.0.0.1', '::1');

-- Revoke all from a user and re-grant only what's needed
REVOKE ALL PRIVILEGES ON *.* FROM 'appuser'@'localhost';
GRANT SELECT, INSERT, UPDATE ON mydb.* TO 'appuser'@'localhost';

FLUSH PRIVILEGES;
```

### Useful Security Queries
```sql
-- Show all user privileges
SHOW GRANTS FOR 'username'@'host';

-- Find users with all privileges
SELECT user, host FROM mysql.user WHERE Super_priv = 'Y';

-- Check if logging is enabled
SHOW VARIABLES LIKE 'general_log';
SHOW VARIABLES LIKE 'slow_query_log';

-- Enable binary logging (for audit trail)
-- In my.cnf: log_bin = /var/log/mysql/mysql-bin.log
```

### SQL Injection Prevention (MySQL)
```php
// Prepared statements — the correct approach
$stmt = $pdo->prepare("SELECT * FROM users WHERE username = ? AND password = ?");
$stmt->execute([$username, $password]);

// Never do this
$query = "SELECT * FROM users WHERE username = '$username'";
```

---

## PostgreSQL

### Hardening Checklist
```bash
# pg_hba.conf — controls client authentication
# Default is trust (no password) — change to md5 or scram-sha-256
# /etc/postgresql/{version}/main/pg_hba.conf

# postgresql.conf — change listen address from * to specific IP
listen_addresses = 'localhost'

# Disable pg_stat_statements exposure to non-superusers
```

```sql
-- Create application user with minimal privileges
CREATE USER appuser WITH PASSWORD 'strongpassword';
GRANT CONNECT ON DATABASE mydb TO appuser;
GRANT USAGE ON SCHEMA public TO appuser;
GRANT SELECT, INSERT, UPDATE ON ALL TABLES IN SCHEMA public TO appuser;

-- Check roles
\du   -- in psql

-- Check database permissions
\l

-- Audit: find superusers
SELECT usename, usesuper FROM pg_user WHERE usesuper = true;

-- Enable logging
ALTER SYSTEM SET log_connections = 'on';
ALTER SYSTEM SET log_disconnections = 'on';
ALTER SYSTEM SET log_statement = 'all';   -- Verbose — use 'ddl' in production
SELECT pg_reload_conf();
```

---

## Microsoft SQL Server

### Hardening Checklist
```sql
-- Disable SA account if not needed
ALTER LOGIN sa DISABLE;

-- Check for accounts with sysadmin role
SELECT name FROM sys.server_principals
WHERE IS_SRVROLEMEMBER('sysadmin', name) = 1;

-- Disable xp_cmdshell (allows OS command execution)
EXEC sp_configure 'xp_cmdshell', 0;
RECONFIGURE;

-- Disable CLR integration if not needed
EXEC sp_configure 'clr enabled', 0;
RECONFIGURE;

-- Disable remote admin connections
EXEC sp_configure 'remote admin connections', 0;
RECONFIGURE;

-- Check linked servers (potential lateral movement)
SELECT name, product, provider FROM sys.servers WHERE is_linked = 1;

-- Audit logins
SELECT name, type_desc, is_disabled, create_date
FROM sys.server_principals
WHERE type IN ('S', 'U', 'G')
ORDER BY create_date DESC;
```

### SQL Server Attack Surface (for pen tests)
```sql
-- xp_cmdshell — execute OS commands (if enabled)
EXEC xp_cmdshell 'whoami';

-- Linked server abuse
EXEC ('SELECT * FROM master..sysdatabases') AT [linked_server_name];

-- Impersonation
EXECUTE AS LOGIN = 'sa';
SELECT SYSTEM_USER;
REVERT;
```

---

## Encryption at Rest

| Database | Method |
|----------|--------|
| MySQL | InnoDB tablespace encryption, MySQL Enterprise TDE |
| PostgreSQL | pgcrypto extension, filesystem encryption (LUKS) |
| SQL Server | Transparent Data Encryption (TDE) |
| SQLite | SQLCipher (third-party) |

```sql
-- SQL Server TDE
CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'StrongPassword!';
CREATE CERTIFICATE TDECert WITH SUBJECT = 'TDE Certificate';
CREATE DATABASE ENCRYPTION KEY WITH ALGORITHM = AES_256 ENCRYPTION BY SERVER CERTIFICATE TDECert;
ALTER DATABASE mydb SET ENCRYPTION ON;
```

---

## Encryption in Transit

```bash
# MySQL — require SSL
# In my.cnf:
# require_secure_transport = ON

# Verify SSL in MySQL
SHOW VARIABLES LIKE '%ssl%';

# PostgreSQL — require SSL in pg_hba.conf
# hostssl  all  all  0.0.0.0/0  scram-sha-256

# SQL Server — force encryption in connection string
# Encrypt=Yes;TrustServerCertificate=No
```

---

## Principle of Least Privilege

Every application should connect with a database account that has only the permissions it needs:

| Application Type | Permissions Needed |
|-----------------|-------------------|
| Read-only API | SELECT only |
| Standard CRUD app | SELECT, INSERT, UPDATE, DELETE |
| App with schema changes | + ALTER, CREATE |
| Admin/migration tool | Full schema rights (never for app user) |
| Backup user | SELECT + LOCK TABLES (MySQL) / pg_dump role |

---

## Auditing and Logging

```sql
-- MySQL: General query log (high volume — use carefully)
SET GLOBAL general_log = 'ON';
SET GLOBAL general_log_file = '/var/log/mysql/general.log';

-- PostgreSQL: Log all DDL statements
ALTER SYSTEM SET log_statement = 'ddl';
SELECT pg_reload_conf();

-- SQL Server: Server Audit
CREATE SERVER AUDIT MyAudit
TO FILE (FILEPATH = 'C:\Audits\');
ALTER SERVER AUDIT MyAudit WITH (STATE = ON);
```

---

## Key Terms

| Term | Definition |
|------|------------|
| TDE | Transparent Data Encryption — encrypts data files at rest |
| SQL Injection | Attack that inserts malicious SQL into a query |
| Prepared Statement | Parameterized query that separates code from data |
| Least Privilege | Granting only the minimum access required |
| pg_hba.conf | PostgreSQL host-based authentication configuration file |
| xp_cmdshell | SQL Server stored procedure that runs OS commands |
| Linked Server | SQL Server feature connecting to a remote DB server |

## Sources

OWASP Foundation. (2021). *SQL injection prevention cheat sheet*. https://cheatsheetseries.owasp.org/cheatsheets/SQL_Injection_Prevention_Cheat_Sheet.html

Center for Internet Security. (2023). *CIS Microsoft SQL Server benchmark*. https://www.cisecurity.org/benchmark/microsoft_sql_server

PostgreSQL Global Development Group. (2024). *PostgreSQL documentation: Client authentication*. https://www.postgresql.org/docs/current/client-authentication.html

---
<- [[Reference-MOC]]
