# 📄 `day-32-volumes-networking.md`

---

# Day 32 – Docker Volumes & Networking

---

# 🔹 Task 1: The Problem – Ephemeral Containers

## Run PostgreSQL Container

```bash
docker run -d \
  --name pg-test \
  -e POSTGRES_PASSWORD=secret \
  postgres
```

Enter container:

```bash
docker exec -it pg-test psql -U postgres
```

Create table:

```sql
CREATE TABLE demo (id SERIAL PRIMARY KEY, name TEXT);
INSERT INTO demo (name) VALUES ('Shibnath');
SELECT * FROM demo;
```

Stop and remove:

```bash
docker stop pg-test
docker rm pg-test
```

Run new container:

```bash
docker run -d \
  --name pg-test2 \
  -e POSTGRES_PASSWORD=secret \
  postgres
```

Check table again.

### Result:

❌ Data is gone.

### Why?

Containers are ephemeral.
Without a persistent storage mechanism, database files are stored inside the container’s writable layer — which is deleted when the container is removed.

---

# 🔹 Task 2: Named Volumes (Persistent Storage)

## Create Named Volume

```bash
docker volume create pg-data
```

Verify:

```bash
docker volume ls
```
<img width="755" height="72" alt="image" src="https://github.com/user-attachments/assets/994596eb-27ae-4d1b-a4b5-32335d0a738b" />

---

## Run PostgreSQL with Volume

```bash
docker run -d \
  --name pg-vol \
  -e POSTGRES_PASSWORD=secret \
  -v pg-data:/var/lib/postgresql/data \
  postgres:16
```
<img width="1161" height="495" alt="image" src="https://github.com/user-attachments/assets/5594f3e6-2864-4bb9-b112-0f429be2f6b7" />

Create data again.

Stop and remove:

```bash
docker stop pg-vol
docker rm pg-vol
```

---

## Run New Container with Same Volume

```bash
docker run -d \
  --name pg-vol2 \
  -e POSTGRES_PASSWORD=secret \
  -v pg-data:/var/lib/postgresql/data \
  postgres:16
```

Check table:

✔ Data still exists.

---

## Inspect Volume

```bash
docker volume inspect pg-data
```
<img width="811" height="204" alt="image" src="https://github.com/user-attachments/assets/eb5e113c-0858-409d-aeab-ed88ea3ba5c2" />

### Why This Works

Named volumes exist independently of containers.
Deleting the container does not delete the volume.

---

# 🔹 Task 3: Bind Mounts

## Create Host Folder

```bash
mkdir my-site
echo "<h1>Live Edit Test</h1>" > my-site/index.html
```

---

## Run Nginx with Bind Mount

```bash
docker run -d \
  -p 8080:80 \
  -v $(pwd)/my-site:/usr/share/nginx/html \
  --name nginx-bind \
  nginx
```

Visit:

```
http://localhost:8080
```
<img width="1086" height="439" alt="image" src="https://github.com/user-attachments/assets/bf2084e6-d366-4fe8-a7e6-fa49f73c337b" />

Edit file on host:

```bash
nano my-site/index.html
```

Refresh browser.

✔ Changes reflect immediately.

---

## Named Volume vs Bind Mount

| Named Volume                    | Bind Mount                |
| ------------------------------- | ------------------------- |
| Managed by Docker               | Managed by host           |
| Stored in Docker’s storage path | Uses exact host directory |
| Portable                        | Host-dependent            |
| Safer for production            | Great for development     |

Bind mounts are ideal for development.
Named volumes are safer for databases in production.

---

# 🔹 Task 4: Docker Networking Basics

---

## List Networks

```bash
docker network ls
```
<img width="702" height="108" alt="image" src="https://github.com/user-attachments/assets/dd28d614-a561-4385-8418-63ec1b9c1021" />

Default networks:

* bridge
* host
* none

---

## Inspect Default Bridge

```bash
docker network inspect bridge
```

---

## Run Two Containers on Default Bridge

```bash
docker run -dit --name c1 ubuntu bash
docker run -dit --name c2 ubuntu bash
```

Try ping by name:

```bash
docker exec c1 ping c2
```

❌ Fails.

Try by IP (find IP via `docker inspect c2`):

```bash
docker exec c1 ping <c2-ip>
```

✔ Works.

---

# 🔹 Task 5: Custom Bridge Network

---

## Create Custom Network

```bash
docker network create my-app-net
```

---

## Run Containers on Custom Network

```bash
docker run -dit --name app1 --network my-app-net ubuntu bash
docker run -dit --name app2 --network my-app-net ubuntu bash
```

Ping by name:

```bash
docker exec app1 ping app2
```

✔ Works.

---

## Why Does Custom Network Allow Name Resolution?

Custom bridge networks include an internal DNS server.

Default bridge:

* Does not support automatic name-based resolution (unless linked manually).

User-defined bridge:

* Provides automatic DNS-based container name resolution.

This is why production setups use custom networks.

---

# 🔹 Task 6: Full Mini App Setup

---

## Create Network

```bash
docker network create app-network
```

---

## Run Database with Volume

```bash
docker volume create mysql-data
```

```bash
docker run -d \
  --name mysql-db \
  --network app-network \
  -e MYSQL_ROOT_PASSWORD=secret \
  -v mysql-data:/var/lib/mysql \
  mysql
```

---

## Run App Container

Example using Ubuntu for test:

```bash
docker run -dit \
  --name app-client \
  --network app-network \
  ubuntu bash
```

Inside app container:

```bash
apt update && apt install -y mysql-client
mysql -h mysql-db -u root -p
```

✔ Connection works using container name.

---

# 🔥 Key Learnings

1️⃣ Containers lose data unless you use volumes
2️⃣ Named volumes persist independently
3️⃣ Bind mounts reflect host changes instantly
4️⃣ Custom networks enable service-to-service communication
5️⃣ Docker includes internal DNS on user-defined networks



