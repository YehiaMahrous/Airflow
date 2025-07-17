# Apache Airflow 2.9.1 with Docker (Windows) — Setup Guide
Overview
This guide walks you through installing and running Apache Airflow 2.9.1 in Docker using the official docker-compose.yaml file.

Tested on Windows 10/11 PowerShell
Uses Python + Docker + Docker Compose
Airflow version: 2.9.1
Prerequisites
- Docker Desktop installed and running
- Python 3.7+ (used to generate a Fernet key)
- Git Bash or PowerShell
Step 1: Clean up any old Airflow project (if needed) (Terminal)
If you previously installed Airflow using Docker:

```
docker compose down --volumes --remove-orphans
docker volume prune -f
```
Step 2: Create a new project folder (Terminal)
```
mkdir airflow-docker
cd airflow-docker
```
Step 3: Download the official Airflow Docker Compose file (terminal)
```
Invoke-WebRequest `
  -Uri 'https://airflow.apache.org/docs/apache-airflow/2.9.1/docker-compose.yaml' `
  -OutFile 'docker-compose.yaml'
```
Step 4: Create the .env file
Create a `.env` file inside airflow-docker with the following content.

Step A: Generate a Fernet key

```
python -c "from cryptography.fernet import Fernet; print(Fernet.generate_key().decode())"
```

Copy the output (e.g., `TIBXUcnw-_CcJNUcfKhcPhDxtzfJJK71_5BKohbe6fs=`)

Step B: Create the file

Example `.env` file:
```
AIRFLOW_UID=50000
AIRFLOW__CORE__FERNET_KEY=ZABXUcnw-_CcJNUcfKhcPhDxtzfJJK71_5BKihie6Pc=
```
Step 5: Create required folders (terminal)
```
mkdir dags, logs, plugins
```
Step 6: Initialize the Airflow metadata database (Terminal)
This command sets up the database schema (tables, indexes, etc.):

```
docker compose up airflow-init
```

You should see logs ending with:
```
Creating tables ... done
```

If there are errors, revisit `.env` or volume cleanup.
Step 7: Start all Airflow services (Terminal)
```
docker compose up -d
```

This will start:
- airflow-webserver
- airflow-scheduler
- airflow-worker
- airflow-triggerer
- postgres (Airflow metadata DB)
- redis (Celery broker)
Step 8: Verify everything is running (Terminal)
```
docker compose ps
```

Wait about 60 seconds until all services show:
```
STATUS: Up (healthy)
```
Step 9: Access the Airflow web UI
Visit: http://localhost:8080

Login credentials (default) :
- Username: airflow
- Password: airflow
Common Docker Commands
| Command                         | Description                            |
|----------------------------------|----------------------------------------|
| docker compose up -d            | Start all containers in the background |
| docker compose down             | Stop all containers                    |
| docker compose ps               | Show container status                  |
| docker compose logs -f          | Follow logs from all containers        |
| docker compose restart          | Restart all services                   |
Optional: Reset Everything (Terminal)
```
docker compose down --volumes --remove-orphans
docker volume prune -f
```
Credits
This setup follows the official Apache Airflow Docker documentation:
https://airflow.apache.org/docs/apache-airflow/stable/howto/docker-compose/index.html
