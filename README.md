# SQL Server 2022 on Docker for macOS

Run SQL Server 2022 in Docker on macOS and restore the AdventureWorks sample database using DBeaver.

## Prerequisites

- Docker Desktop
- DBeaver
- AdventureWorks2022.bak

---

## Step 1 - Pull SQL Server

```bash
docker pull mcr.microsoft.com/mssql/server:2022-latest
```

---

## Step 2 - Create the Container

```bash
docker run -d \
  --platform linux/amd64 \
  --name sqlserver \
  -e ACCEPT_EULA=Y \
  -e MSSQL_PID=Developer \
  -e MSSQL_SA_PASSWORD='MyStrong@Pass2026' \
  -p 1433:1433 \
  mcr.microsoft.com/mssql/server:2022-latest
```

---

## Step 3 - Verify SQL Server

```bash
docker logs -f sqlserver
```

Wait until you see:

```
SQL Server is now ready for client connections.
```

---

## Step 4 - Connect with DBeaver

| Setting | Value |
|---------|-------|
| Host | localhost |
| Port | 1433 |
| Database | master |
| User | sa |
| Password | MyStrong@Pass2026 |

Enable **Trust Server Certificate**.

---

## Step 5 - Copy the Backup

Create a backup folder:

```bash
docker exec -it sqlserver mkdir -p /var/opt/mssql/backup
```

Copy the backup:

```bash
docker cp AdventureWorks2022.bak sqlserver:/var/opt/mssql/backup/
```

---

## Step 6 - Get Logical File Names

Run in DBeaver:

```sql
RESTORE FILELISTONLY
FROM DISK='/var/opt/mssql/backup/AdventureWorks2022.bak';
```

---

## Step 7 - Restore the Database

Replace the logical names if they are different.

```sql
RESTORE DATABASE AdventureWorks2022
FROM DISK='/var/opt/mssql/backup/AdventureWorks2022.bak'
WITH
MOVE 'AdventureWorks2022' TO '/var/opt/mssql/data/AdventureWorks2022.mdf',
MOVE 'AdventureWorks2022_log' TO '/var/opt/mssql/data/AdventureWorks2022_log.ldf',
REPLACE,
RECOVERY;
```

---

## Step 8 - Verify

```sql
SELECT name
FROM sys.databases;
```

You should see:

- AdventureWorks2022

---

## Useful Commands

Start

```bash
docker start sqlserver
```

Stop

```bash
docker stop sqlserver
```

Logs

```bash
docker logs -f sqlserver
```

Remove

```bash
docker rm -f sqlserver
```

---

## Troubleshooting

### Connection refused

```bash
docker ps
```

If the container is not running:

```bash
docker start sqlserver
```

### Container exits with code 137

Increase Docker Desktop memory to at least **6 GB** and restart Docker.

## Author

- website: [Menayer.com](https://www.menayer.com)
- e-mail: <Amr@menayer.com>
