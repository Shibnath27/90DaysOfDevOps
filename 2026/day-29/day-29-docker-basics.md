# 📄 `day-29-docker-basics.md`

---

# Day 29 – Introduction to Docker

---

# 🔹 Task 1: What is Docker?

## What is a Container?

A container is a lightweight, isolated runtime environment that packages:

* Application code
* Dependencies
* Libraries
* Runtime
* Configuration

It ensures the application runs the same way everywhere.

### Why Do We Need Containers?

Before containers:

* “It works on my machine” problems
* Dependency conflicts
* Difficult deployments
* Heavy VM overhead

Containers solve:

* Environment consistency
* Faster deployment
* Better resource utilization
* Portability across environments

---

# Containers vs Virtual Machines

| Feature        | Containers           | Virtual Machines             |
| -------------- | -------------------- | ---------------------------- |
| OS             | Share host OS kernel | Full guest OS                |
| Size           | MBs                  | GBs                          |
| Startup Time   | Seconds              | Minutes                      |
| Resource Usage | Lightweight          | Heavy                        |
| Isolation      | Process-level        | Full hardware virtualization |

### Real Difference

VM:

* Virtualizes hardware.

Container:

* Virtualizes the OS layer.

Containers share the host kernel but isolate processes using namespaces and cgroups.

---

# Docker Architecture

Docker follows a client-server architecture.

### Components

1️⃣ Docker Client

* CLI (`docker run`, `docker ps`)
* Sends commands to daemon

2️⃣ Docker Daemon (`dockerd`)

* Runs in background
* Builds images
* Runs containers
* Manages networking & storage

3️⃣ Docker Images

* Read-only templates
* Used to create containers

4️⃣ Docker Containers

* Running instance of an image

5️⃣ Docker Registry

* Stores images
* Example: Docker Hub

---

## Architecture in Simple Words

You type a command →
Docker client sends request →
Docker daemon pulls image from registry (if needed) →
Daemon creates container →
Container runs on host OS.

---

# 🔹 Task 2: Install Docker

## Install (Ubuntu Example)

```bash
sudo apt update
sudo apt install docker.io -y
```

Start service:

```bash
sudo systemctl start docker
sudo systemctl enable docker
```

Verify:

```bash
docker --version
sudo docker run hello-world
```
<img width="1017" height="404" alt="image" src="https://github.com/user-attachments/assets/fc8c805e-fd76-49ed-85c1-ad39fec59712" />


### What `hello-world` Did

* Pulled image from Docker Hub
* Created container
* Executed program inside container
* Printed confirmation message
* Exited

This verified:

* Client ↔ daemon communication
* Image pull
* Container execution

---

# 🔹 Task 3: Run Real Containers

---

## Run Nginx Container

```bash
docker run -d -p 8080:80 --name my-nginx nginx
```
<img width="1132" height="268" alt="image" src="https://github.com/user-attachments/assets/b13495cb-bc24-4a92-8203-01165ff87bf1" />

Explanation:

* `-d` → Detached mode
* `-p 8080:80` → Map host port 8080 to container port 80
* `--name` → Custom container name

Access in browser:

```
http://localhost:8080
```

You should see Nginx welcome page.

---

## Run Ubuntu in Interactive Mode

```bash
docker run -it ubuntu bash
```

Now you're inside a minimal Linux environment.

Try:

```bash
ls
apt update
whoami
```
<img width="886" height="432" alt="image" src="https://github.com/user-attachments/assets/7b8686ed-882e-4d9c-bb50-07ca9c14400b" />

Exit with:

```bash
exit
```

---

## List Running Containers

```bash
docker ps
```

---

## List All Containers

```bash
docker ps -a
```

---

## Stop a Container

```bash
docker stop my-nginx
```
<img width="1081" height="120" alt="image" src="https://github.com/user-attachments/assets/c3682bc5-3f57-4247-a50f-cfaab3cb4c44" />

---

## Remove a Container

```bash
docker rm my-nginx
```

---

# 🔹 Task 4: Explore Docker Behavior

---

## Detached Mode vs Interactive Mode

* `-it` → Interactive terminal attached
* `-d` → Runs in background

Detached containers do not block your terminal.

---

## Give Custom Name

```bash
docker run -d --name web-server nginx
```

---

## Port Mapping

```bash
docker run -d -p 9090:80 nginx
```

Host port 9090 → Container port 80

---

## Check Logs

```bash
docker logs web-server
```

<img width="1077" height="296" alt="image" src="https://github.com/user-attachments/assets/7f40e1d7-282f-4c88-b811-f78d2e6656a2" />


Useful for debugging.

---

## Execute Command in Running Container

```bash
docker exec -it web-server bash
```

This attaches to running container.

---

# 🔥 Key Observations

* Containers are ephemeral by default.
* If container is deleted, changes inside it are lost.
* Containers are processes, not full VMs.
* Docker simplifies application deployment drastically.

