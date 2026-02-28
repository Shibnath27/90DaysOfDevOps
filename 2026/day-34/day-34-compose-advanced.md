# 📄 `day-34-compose-advanced.md`

---

# Day 34 – Docker Compose: Real-World Multi-Container Apps

Today I built a production-style 3-tier stack:

* Web Application (Flask)
* PostgreSQL Database
* Redis Cache

This setup includes:

* Custom Dockerfile
* Healthchecks
* Service dependencies
* Restart policies
* Named networks
* Named volumes
* Scaling experiment

---

# 📁 Project Structure

```
day-34-compose-advanced/
│
├── docker-compose.yml
└── app/
    ├── Dockerfile
    ├── requirements.txt
    └── app.py
```

---

# 🔹 Task 1: Build 3-Service Stack

---

## 🔹 app/app.py

```python
from flask import Flask
import os
import psycopg2
import redis

app = Flask(__name__)

@app.route("/")
def hello():
    return "Hello from Flask App connected to Postgres & Redis!"

if __name__ == "__main__":
    app.run(host="0.0.0.0", port=5000)
```

---

## 🔹 app/requirements.txt

```
flask
psycopg2-binary
redis
```

---

## 🔹 app/Dockerfile

```dockerfile
FROM python:3.11-slim

WORKDIR /app

COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

COPY . .

EXPOSE 5000

CMD ["python", "app.py"]
```

---

# 🔹 docker-compose.yml

```yaml
version: "3.9"

services:
  db:
    image: postgres:15
    container_name: postgres-db
    restart: always
    environment:
      POSTGRES_USER: appuser
      POSTGRES_PASSWORD: secret
      POSTGRES_DB: appdb
    volumes:
      - postgres_data:/var/lib/postgresql/data
    networks:
      - app-network
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U appuser"]
      interval: 5s
      timeout: 5s
      retries: 5

  cache:
    image: redis:7
    container_name: redis-cache
    restart: always
    networks:
      - app-network

  web:
    build: ./app
    container_name: flask-app
    ports:
      - "5000:5000"
    depends_on:
      db:
        condition: service_healthy
      cache:
        condition: service_started
    networks:
      - app-network
    labels:
      project: "day-34"
      environment: "dev"

volumes:
  postgres_data:

networks:
  app-network:
    driver: bridge
```

---

# 🔹 Task 2: depends_on & Healthchecks

### What Happens?

* PostgreSQL starts first.
* Healthcheck runs `pg_isready`.
* Only after DB becomes healthy does Flask start.

Test:

```
docker compose down
docker compose up
```

You will observe:

Flask waits until Postgres passes healthcheck.

Without `condition: service_healthy`, the app may start before DB is ready.

---

# 🔹 Task 3: Restart Policies

### restart: always

If database container crashes:

```
docker kill postgres-db
```

It automatically restarts.

Use case:

* Databases
* Critical backend services

---

### restart: on-failure

Restarts only if container exits with non-zero code.

Use case:

* Batch jobs
* Workers
* Services that shouldn't restart if manually stopped

Difference:

| Policy     | Manual Stop | Crash    |
| ---------- | ----------- | -------- |
| always     | Restarts    | Restarts |
| on-failure | Does NOT    | Restarts |

---

# 🔹 Task 4: Custom Dockerfile with build:

Compose builds app automatically:

```
docker compose up --build
```

If you modify `app.py`, rebuild with:

```
docker compose up --build
```

Single command rebuild + restart.

---

# 🔹 Task 5: Named Networks & Volumes

Explicit network defined:

```
networks:
  app-network:
```

All services communicate using service names:

* db
* cache
* web

Volume:

```
postgres_data
```

Data persists across:

```
docker compose down
docker compose up
```

Remove volume explicitly:

```
docker compose down -v
```

---

# 🔹 Task 6: Scaling (Bonus)

Run:

```
docker compose up --scale web=3
```

### What Happens?

Error or port conflict.

Why?

Because:

```
ports:
  - "5000:5000"
```

Host port 5000 can only bind once.

Scaling multiple replicas requires:

* Reverse proxy (Nginx / Traefik)
* Load balancer
* No static port mapping per replica

This limitation explains why Compose scaling is not production-grade orchestration.

Kubernetes solves this with:

* Services
* Load balancing
* Internal networking abstraction

---

# 🔥 Key Learnings

* Healthchecks make startup deterministic.
* depends_on alone is insufficient without service_healthy.
* Restart policies improve resilience.
* Explicit networks improve clarity.
* Scaling requires load balancing strategy.

