# 📄 `day-36-docker-project.md`

---

# Day 36 – Docker Project: End-to-End Application Dockerization

Today I Dockerized a complete production-style Flask application with PostgreSQL using:

* Multi-stage Dockerfile
* Non-root user
* Docker Compose
* Named volumes
* Custom network
* Environment variables via `.env`
* Healthchecks
* Docker Hub image push

This simulates real-world containerized application deployment.

---

# 📁 Project Structure

```
day-36-docker-project/
│
├── app/
│   ├── app.py
│   ├── requirements.txt
│   ├── Dockerfile
│   └── .dockerignore
│   └── templates/
│           └──index.html
├── docker-compose.yml
├── .env
└── README.md
```

---

# 🔹 Application Overview

Simple Flask API that connects to PostgreSQL and returns database status.

---

## 🔹 app/app.py

```python
from flask import Flask, render_template, os
import psycopg2

app = Flask(__name__)

def get_db_status():
    try:
        conn = psycopg2.connect(
            host=os.getenv("DB_HOST"),
            database=os.getenv("POSTGRES_DB"),
            user=os.getenv("POSTGRES_USER"),
            password=os.getenv("POSTGRES_PASSWORD"),
            connect_timeout=3
        )
        conn.close()
        return True, "System Operational"
    except Exception as e:
        return False, str(e)

@app.route("/")
def home():
    success, message = get_db_status()
    return render_template("index.html", success=success, message=message)

if __name__ == "__main__":
    app.run(host="0.0.0.0", port=5000)
```

---

## 🔹 requirements.txt

```
flask
psycopg2-binary
gunicorn
```

---

# 🔹 Multi-Stage Dockerfile

---

## 🔹 app/Dockerfile

```dockerfile
# Stage 1: Builder
FROM python:3.11-slim AS builder

WORKDIR /app

COPY requirements.txt .
# Install to a prefix directory instead of --user
RUN pip install --no-cache-dir --prefix=/install -r requirements.txt
#RUN pip install --user --no-cache-dir -r requirements.txt

# If you are using a non-root user, make sure their local bin is in the PATH
#ENV PATH="/home/appuser/.local/bin:${PATH}"

COPY . .

# Stage 2: Runtime
FROM python:3.11-slim

WORKDIR /app

RUN addgroup --system appgroup && adduser --system appuser --ingroup appgroup

COPY --from=builder /install /usr/local
COPY --from=builder /app /app

#ENV PATH=/home/appuser/.local/bin:$PATH
# Ensure appuser owns the app directory
RUN chown -R appuser:appgroup /app
USER appuser

EXPOSE 5000

CMD ["gunicorn", "--bind", "0.0.0.0:5000", "app:app"]
```

---

# 🔹 .dockerignore

```
__pycache__
*.pyc
*.pyo
.env
.git
.gitignore
```

---
---
# 🔹 index.html

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>System Monitor | Dashboard</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@300;400;600&display=swap" rel="stylesheet">
    <style>
        body { font-family: 'Inter', sans-serif; }
        .glass { background: rgba(255, 255, 255, 0.95); backdrop-filter: blur(10px); }
    </style>
</head>
<body class="bg-slate-50 min-h-screen flex items-center justify-center p-6">

    <div class="max-w-md w-full glass rounded-3xl shadow-2xl overflow-hidden border border-white">
        <div class="bg-slate-900 p-8 text-white">
            <h1 class="text-2xl font-600 tracking-tight">Cloud Infrastructure</h1>
            <p class="text-slate-400 text-sm mt-1">Real-time connection monitor</p>
        </div>

        <div class="p-8">
            <div class="flex items-center justify-between mb-8">
                <div>
                    <p class="text-xs font-bold uppercase tracking-wider text-slate-400">Database Engine</p>
                    <p class="text-lg font-semibold text-slate-800">PostgreSQL Cluster</p>
                </div>
                <div class="h-12 w-12 bg-slate-100 rounded-xl flex items-center justify-center">
                    <svg class="w-6 h-6 text-slate-600" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M4 7v10c0 2.21 3.58 4 8 4s8-1.79 8-4V7M4 7c0 2.21 3.58 4 8 4s8-1.79 8-4M4 7c0-2.21 3.58-4 8-4s8 1.79 8 4m0 5c0 2.21-3.58 4-8 4s-8-1.79-8-4"></path></svg>
                </div>
            </div>

            <div class="rounded-2xl p-6 {% if success %}bg-emerald-50 border border-emerald-100{% else %}bg-rose-50 border border-rose-100{% endif %}">
                <div class="flex items-center gap-4">
                    <span class="relative flex h-3 w-3">
                        <span class="animate-ping absolute inline-flex h-full w-full rounded-full {% if success %}bg-emerald-400{% else %}bg-rose-400{% endif %} opacity-75"></span>
                        <span class="relative inline-flex rounded-full h-3 w-3 {% if success %}bg-emerald-500{% else %}bg-rose-500{% endif %}"></span>
                    </span>
                    <h2 class="text-sm font-semibold {% if success %}text-emerald-800{% else %}text-rose-800{% endif %}">
                        {% if success %}Connected Successfully{% else %}Connection Failed{% endif %}
                    </h2>
                </div>
                <p class="mt-2 text-sm {% if success %}text-emerald-600{% else %}text-rose-600{% endif %} leading-relaxed">
                    {{ message }}
                </p>
            </div>
        </div>

        <div class="px-8 py-4 bg-slate-50 border-t border-slate-100 flex justify-between items-center text-[10px] text-slate-400 uppercase tracking-widest font-bold">
            <span>Region: us-east-1</span>
            <span>Port: 5000</span>
        </div>
    </div>

</body>
</html>
```
---
# 🔹 docker-compose.yml

```yaml
version: "3.9"

services:
  db:
    image: postgres:15-alpine
    restart: always
    env_file:
      - .env
    volumes:
      - postgres_data:/var/lib/postgresql/data
    networks:
      - app-network
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U ${POSTGRES_USER}"]
      interval: 5s
      timeout: 5s
      retries: 5

  web:
    build: ./app
    ports:
      - "5000:5000"
    env_file:
      - .env
    depends_on:
      db:
        condition: service_healthy
    networks:
      - app-network

volumes:
  postgres_data:

networks:
  app-network:
    driver: bridge
```

---

# 🔹 .env

```
POSTGRES_USER=appuser
POSTGRES_PASSWORD=secret
POSTGRES_DB=appdb
DB_HOST=db
```

---

# 🔹 Run the Stack

```bash
docker compose up --build -d
```

Access:

```
http://localhost:5000
```
<img width="1362" height="685" alt="image" src="https://github.com/user-attachments/assets/2dd5b39f-b59f-4c20-ab27-ac6c585071f0" />

---

# 🔹 Tag & Push to Docker Hub

Login:

```bash
docker login
```
Tag an image using its Image ID:

```bash
docker tag 0cf00315dcbe yourusername/flask-psql-monitor:latest
```
Push:

```bash
docker push yourusername/flask-psql-monitor:latest
```

---

# 🔹 Test Clean Deployment

Remove everything:

```bash
docker compose down -v
docker system prune -a
```

Run from Hub: Update your docker-compose.yml to use image: yourusername/flask-psql-monitor:latest instead of the build: . context, then run:
```bash
docker compose up -d
```

Verified application works fresh.

---

# 🔹 README.md (Summary)

# 🚀 Flask + PostgreSQL System Monitor

A containerized Flask application that monitors database connectivity with a modern, responsive UI. Built for high-reliability environments and optimized for small image footprints.

## ✨ Features
* **Multi-stage Docker Build**: Final image size is only 142MB.
* **Security**: Runs as a non-root user (`appuser`).
* **UI**: Modern dashboard using Tailwind CSS and Flask templates.
* **Resilience**: Docker Compose health checks ensure the app only starts once the DB is ready.

## 🛠️ Prerequisites
* Docker and Docker Compose installed.
* A `.env` file in the root directory.

## 🚀 Quick Start
1. **Configure Environment**: Create a `.env` file:
   ```env
   POSTGRES_USER=admin
   POSTGRES_PASSWORD=secretpassword
   POSTGRES_DB=monitor_db
2. Launch the Stack:
    ```
        docker compose up -d
    ```
3. Access the App: Open your browser and navigate to http://localhost:5000.

## What This App Does

Simple Flask API that connects to PostgreSQL and validates DB connectivity.

## Required Environment Variables

Defined in `.env`:

* POSTGRES_USER
* POSTGRES_PASSWORD
* POSTGRES_DB
* DB_HOST

## Docker Hub Link: https://hub.docker.com/r/shibnath27/flask-psql-monitor

---

# 🔥 Key Production Concepts Implemented

* Multi-stage builds for smaller image
* Non-root container user
* Gunicorn instead of Flask dev server
* Healthchecks with dependency conditions
* Named volume for persistence
* Explicit custom network
* Environment variable separation
* Image distribution via Docker Hub

