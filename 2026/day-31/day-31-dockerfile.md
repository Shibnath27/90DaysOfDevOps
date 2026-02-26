# 📄 `day-31-dockerfile.md`

---

# Day 31 – Dockerfile: Build Your Own Images

---

# 🔹 Task 1: First Dockerfile

## Step 1: Create Project Folder

```bash
mkdir my-first-image
cd my-first-image
```

---

## Step 2: Create Dockerfile

```dockerfile
FROM ubuntu:latest

RUN apt update && apt install -y curl

CMD ["echo", "Hello from my custom image!"]
```

---

## Step 3: Build Image

```bash
docker build -t my-ubuntu:v1 .
```

> `.` = build context (current directory)

---

## Step 4: Run Container

```bash
docker run my-ubuntu:v1
```

Output:

```
Hello from my custom image!
```

✔ Verified custom image works.

---

# 🔹 Task 2: Dockerfile Instructions Explained

Create new folder:

```bash
mkdir dockerfile-demo
cd dockerfile-demo
```

Create `index.html`:

```html
<h1>Hello Docker</h1>
```

Create Dockerfile:

```dockerfile
FROM nginx:alpine

WORKDIR /usr/share/nginx/html

COPY index.html .

EXPOSE 80

CMD ["nginx", "-g", "daemon off;"]
```

---

## What Each Instruction Does

* **FROM** → Base image
* **WORKDIR** → Sets working directory inside container
* **COPY** → Copies file from host → image
* **EXPOSE** → Documents container port
* **CMD** → Default runtime command

---

## Build

```bash
docker build -t nginx-demo:v1 .
```

---

## Run

```bash
docker run -d -p 8080:80 nginx-demo:v1
```

Visit:

```
http://localhost:8080
```

Your custom HTML page loads.

---

# 🔹 Task 3: CMD vs ENTRYPOINT

---

## Case 1: CMD

Dockerfile:

```dockerfile
FROM alpine
CMD ["echo", "hello"]
```

Build & run:

```bash
docker run image-name
```

Output:

```
hello
```

Now override:

```bash
docker run image-name ls
```

Output:
Runs `ls` instead of echo.

👉 CMD is easily overridden.

---

## Case 2: ENTRYPOINT

Dockerfile:

```dockerfile
FROM alpine
ENTRYPOINT ["echo"]
```

Run:

```bash
docker run image-name hello world
```

Output:

```
hello world
```

ENTRYPOINT remains fixed. Arguments are appended.

---

## When to Use CMD vs ENTRYPOINT

| Use Case                                             | Recommendation |
| ---------------------------------------------------- | -------------- |
| Default command that user may override               | CMD            |
| Force container to behave like a specific executable | ENTRYPOINT     |
| Build CLI-style tool container                       | ENTRYPOINT     |

In production:
ENTRYPOINT defines the container’s purpose.
CMD provides default arguments.

---

# 🔹 Task 4: Simple Web App Image

Create folder:

```bash
mkdir my-website
cd my-website
```

Create `index.html`:

```html
<!DOCTYPE html>
<html>
<head>
  <title>My Docker Website</title>
</head>
<body>
  <h1>Deployed via Docker!</h1>
</body>
</html>
```

---

Create Dockerfile:

```dockerfile
FROM nginx:alpine

COPY index.html /usr/share/nginx/html/index.html

EXPOSE 80
```

---

Build:

```bash
docker build -t my-website:v1 .
```

Run:

```bash
docker run -d -p 8080:80 --name mysite my-website:v1
```

Visit:

```
http://localhost:8080
```

✔ Custom website running inside container.

---

# 🔹 Task 5: .dockerignore

Create `.dockerignore`:

```
node_modules
.git
*.md
.env
```

Purpose:

* Prevent sensitive files from entering image
* Reduce image size
* Speed up build process

Rebuild and verify:
Ignored files are not copied.

---

# 🔹 Task 6: Build Optimization & Caching

Build once:

```bash
docker build -t test:v1 .
```

Change a file copied in Dockerfile and rebuild.

Observe:
Only layers after the change rebuild.

---

## Why Layer Order Matters

Docker caches layers sequentially.

If you place:

```dockerfile
COPY . .
RUN apt install
```

Every small code change invalidates cache → dependencies reinstall.

Better approach:

```dockerfile
COPY package.json .
RUN npm install
COPY . .
```

Put frequently changing instructions at bottom.

Result:

* Faster rebuilds
* Efficient caching
* Better CI/CD performance

---

# 🔥 Key Learnings

* Dockerfile = blueprint for image creation
* Images are layered
* CMD vs ENTRYPOINT control runtime behavior
* .dockerignore improves security and speed
* Layer ordering directly impacts build efficiency

