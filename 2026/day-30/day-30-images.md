# 📄 `day-30-images.md`

---

# Day 30 – Docker Images & Container Lifecycle

---

# 🔹 Task 1: Docker Images

## Pull Images

```bash
docker pull nginx
docker pull ubuntu
docker pull alpine
```

---

## List Images

```bash
docker images
```

Typical size comparison:

* nginx → ~140MB+
* ubuntu → ~70MB+
* alpine → ~5–7MB

### Why is Alpine Much Smaller?

* Alpine uses musl libc instead of glibc
* Minimal packages installed
* Designed for lightweight containers
* Smaller attack surface

Ubuntu includes many base utilities → larger footprint.

---

## Inspect an Image

```bash
docker inspect nginx
```

Information you can see:

* Image ID
* Architecture
* OS
* Environment variables
* Default command (CMD)
* Layers
* Creation date

---

## Remove an Image

```bash
docker rmi alpine
```

If image is in use by a container → removal will fail.

---

# 🔹 Task 2: Image Layers

```bash
docker image history nginx
```

Output shows:

* Each instruction used to build the image
* Layer sizes
* Some layers show `0B`

### What Are Docker Layers?

Each Dockerfile instruction creates a new **read-only layer**.

Example:

* Base OS
* Install packages
* Copy files
* Set environment variables

### Why Some Layers Show 0B?

Metadata-only changes (e.g., CMD, ENV)
They modify configuration, not filesystem content.

---

## Why Docker Uses Layers

1️⃣ Efficient storage (shared base layers)
2️⃣ Faster builds (layer caching)
3️⃣ Faster image pulls (only missing layers downloaded)
4️⃣ Version control for images

Example:
If 10 images use Ubuntu base → only one Ubuntu layer stored.

---

# 🔹 Task 3: Container Lifecycle

---

## Create Container (Not Started)

```bash
docker create --name lifecycle-test nginx
```

Check:

```bash
docker ps -a
```

State → Created

---

## Start Container

```bash
docker start lifecycle-test
```

State → Running

---

## Pause Container

```bash
docker pause lifecycle-test
```

State → Paused

---

## Unpause

```bash
docker unpause lifecycle-test
```

---

## Stop Container

```bash
docker stop lifecycle-test
```

State → Exited

---

## Restart

```bash
docker restart lifecycle-test
```

---

## Kill

```bash
docker kill lifecycle-test
```

Immediately terminates process.

---

## Remove Container

```bash
docker rm lifecycle-test
```

---

### Lifecycle States

Created → Running → Paused → Stopped → Removed

Containers are ephemeral runtime instances of images.

---

# 🔹 Task 4: Working with Running Containers

---

## Run Nginx Detached

```bash
docker run -d -p 8080:80 --name web nginx
```

---

## View Logs

```bash
docker logs web
```

---

## Follow Logs (Real-Time)

```bash
docker logs -f web
```

---

## Exec Into Container

```bash
docker exec -it web bash
```

Explore:

```bash
ls /
cat /etc/nginx/nginx.conf
```

---

## Run Single Command Without Entering

```bash
docker exec web ls /usr/share/nginx/html
```

---

## Inspect Container

```bash
docker inspect web
```

Find:

* IP address (under NetworkSettings)
* Port mappings
* Mounts
* Environment variables

---

# 🔹 Task 5: Cleanup

---

## Stop All Running Containers

```bash
docker stop $(docker ps -q)
```

---

## Remove All Stopped Containers

```bash
docker container prune
```

---

## Remove Unused Images

```bash
docker image prune
```

---

## Check Docker Disk Usage

```bash
docker system df
```

---

# 🔥 Key Concepts Learned

* Image = Blueprint
* Container = Running instance of image
* Images are layered and cached
* Containers have states
* Docker optimizes storage via shared layers
* Containers are not persistent by default

