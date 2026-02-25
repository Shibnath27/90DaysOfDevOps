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
<img width="740" height="122" alt="image" src="https://github.com/user-attachments/assets/1c8612f7-e17e-4885-bd1a-367808239197" />

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
<img width="1144" height="610" alt="image" src="https://github.com/user-attachments/assets/595fa2d1-50b9-4bbb-adf1-df7e1e7dcd1f" />

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
<img width="1102" height="184" alt="image" src="https://github.com/user-attachments/assets/3a7bdac7-e218-4100-bdf4-a04b267310ff" />

If image is in use by a container → removal will fail.

---

# 🔹 Task 2: Image Layers

```bash
docker image history nginx
```
<img width="1142" height="322" alt="image" src="https://github.com/user-attachments/assets/673ca0dd-0380-484e-ba51-c87f8a3b81fb" />

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
<img width="1124" height="177" alt="image" src="https://github.com/user-attachments/assets/bec86a16-fc38-41e0-a4f1-383e472b8eb0" />

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
<img width="1140" height="172" alt="image" src="https://github.com/user-attachments/assets/cdecb5aa-a2ac-4bad-8111-cdda250af31e" />

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
<img width="1115" height="209" alt="image" src="https://github.com/user-attachments/assets/1ce37bd0-8067-4c63-a75e-d88fbbfc2b6b" />

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
<img width="1110" height="292" alt="image" src="https://github.com/user-attachments/assets/6786e2ea-b4cf-44ca-9f0c-33ecee7c8724" />

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
<img width="872" height="513" alt="image" src="https://github.com/user-attachments/assets/13b50dbc-26da-47fe-968c-e85941811303" />

---

## Run Single Command Without Entering

```bash
docker exec web ls /usr/share/nginx/html
```
<img width="680" height="59" alt="image" src="https://github.com/user-attachments/assets/f44bce3f-be87-4e72-8154-53f98c3016fa" />

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
<img width="760" height="60" alt="image" src="https://github.com/user-attachments/assets/67a91bdc-eac8-4f3f-b0b4-084c838fdf61" />

---

## Remove All Stopped Containers

```bash
docker container prune
```
<img width="644" height="231" alt="image" src="https://github.com/user-attachments/assets/5be2bc0b-0ab7-4140-aa7d-44fe933228b0" />

---

## Remove Unused Images

```bash
docker image prune
```
<img width="844" height="245" alt="image" src="https://github.com/user-attachments/assets/c1c59fa8-b60f-44cf-84bf-285b9eac1bdf" />

---

## Check Docker Disk Usage

```bash
docker system df
```
<img width="600" height="113" alt="image" src="https://github.com/user-attachments/assets/01332f05-a1c8-4914-9885-ab7dd57747b8" />

---

# 🔥 Key Concepts Learned

* Image = Blueprint
* Container = Running instance of image
* Images are layered and cached
* Containers have states
* Docker optimizes storage via shared layers
* Containers are not persistent by default

