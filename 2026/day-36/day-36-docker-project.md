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
│
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
from flask import Flask
import os
import psycopg2

app = Flask(__name__)

@app.route("/")
def home():
    try:
        conn = psycopg2.connect(
            host=os.getenv("DB_HOST"),
            database=os.getenv("POSTGRES_DB"),
            user=os.getenv("POSTGRES_USER"),
            password=os.getenv("POSTGRES_PASSWORD")
        )
        return "Connected to PostgreSQL successfully!"
    except Exception as e:
        return f"Database connection failed: {e}"

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
RUN pip install --user --no-cache-dir -r requirements.txt

COPY . .

# Stage 2: Runtime
FROM python:3.11-slim

WORKDIR /app

RUN addgroup --system appgroup && adduser --system appuser --ingroup appgroup

COPY --from=builder /root/.local /home/appuser/.local
COPY --from=builder /app /app

ENV PATH=/home/appuser/.local/bin:$PATH

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

---

# 🔹 Tag & Push to Docker Hub

Build & tag:

```bash
docker build -t yourusername/flask-app:1.0 ./app
```

Push:

```bash
docker login
docker push yourusername/flask-app:1.0
```

---

# 🔹 Test Clean Deployment

Remove everything:

```bash
docker compose down -v
docker system prune -a
```

Pull image from Docker Hub and run:

```bash
docker compose up -d
```

Verified application works fresh.

---

# 🔹 README.md (Summary)

## What This App Does

Simple Flask API that connects to PostgreSQL and validates DB connectivity.

## Run Instructions

```
docker compose up --build -d
```

## Required Environment Variables

Defined in `.env`:

* POSTGRES_USER
* POSTGRES_PASSWORD
* POSTGRES_DB
* DB_HOST

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

