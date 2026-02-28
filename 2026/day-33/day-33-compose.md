# 📄 `day-33-compose.md`

---

# Day 33 – Docker Compose: Multi-Container Basics

---

# 🔹 Task 1: Install & Verify

## Check Compose Version

```bash
docker compose version
```

If not installed (older systems):

```bash
sudo apt install docker-compose-plugin
```

Docker Compose v2 runs as:

```
docker compose
```

(not `docker-compose`)

---

# 🔹 Task 2: First Compose File (Single Container)

---

## Create Project Folder

```bash
mkdir compose-basics
cd compose-basics
```

---

## Create `docker-compose.yml`

```yaml
services:
  web:
    image: nginx:alpine
    ports:
      - "8080:80"
```

---

## Start Services

```bash
docker compose up -d
```

Access:

```
http://localhost:8080
```

---

## Stop & Remove

```bash
docker compose down
```

Compose automatically:

* Creates a default network
* Manages container lifecycle
* Cleans up when brought down

---

# 🔹 Task 3: Two-Container Setup (WordPress + MySQL)

---

## Create New Folder

```bash
mkdir wordpress-compose
cd wordpress-compose
```

---

## Create `docker-compose.yml`

```yaml
services:
  db:
    image: mysql:8
    container_name: wp-mysql
    restart: always
    environment:
      MYSQL_ROOT_PASSWORD: example
      MYSQL_DATABASE: wordpress
      MYSQL_USER: wpuser
      MYSQL_PASSWORD: wppass
    volumes:
      - db_data:/var/lib/mysql

  wordpress:
    image: wordpress:latest
    container_name: wp-app
    restart: always
    ports:
      - "8081:80"
    environment:
      WORDPRESS_DB_HOST: db
      WORDPRESS_DB_USER: wpuser
      WORDPRESS_DB_PASSWORD: wppass
      WORDPRESS_DB_NAME: wordpress
    depends_on:
      - db

volumes:
  db_data:
```

---

## Start Stack

```bash
docker compose up -d
```

Access:

```
http://localhost:8081
```

Complete WordPress setup.

---

## Verify Persistence

Stop everything:

```bash
docker compose down
```

Start again:

```bash
docker compose up -d
```

✔ WordPress data remains.

Because:

* MySQL uses named volume `db_data`

---

# 🔹 Task 4: Important Compose Commands

---

## Start in Detached Mode

```bash
docker compose up -d
```

---

## View Running Services

```bash
docker compose ps
```

---

## View Logs (All Services)

```bash
docker compose logs -f
```

---

## View Logs (Specific Service)

```bash
docker compose logs -f db
```

---

## Stop Without Removing

```bash
docker compose stop
```

---

## Remove Everything

```bash
docker compose down
```

Remove volumes too:

```bash
docker compose down -v
```

---

## Rebuild Images

```bash
docker compose up -d --build
```

---

# 🔹 Task 5: Environment Variables

---

## Option 1: Directly in YAML

Already used in WordPress example:

```yaml
environment:
  MYSQL_ROOT_PASSWORD: example
```

---

## Option 2: Using `.env` File

Create `.env`:

```
MYSQL_ROOT_PASSWORD=example
MYSQL_DATABASE=wordpress
MYSQL_USER=wpuser
MYSQL_PASSWORD=wppass
```

Modify compose file:

```yaml
environment:
  MYSQL_ROOT_PASSWORD: ${MYSQL_ROOT_PASSWORD}
  MYSQL_DATABASE: ${MYSQL_DATABASE}
  MYSQL_USER: ${MYSQL_USER}
  MYSQL_PASSWORD: ${MYSQL_PASSWORD}
```

---

## Verify Variables

```bash
docker compose config
```

This shows fully rendered configuration.

---

# 🔥 Key Observations

* Compose automatically creates a dedicated network
* Service names act as DNS names
* Volumes are declared once and reused
* Stack can be launched or destroyed with one command
* Infrastructure becomes declarative (YAML-based)

