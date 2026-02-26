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

<img width="1164" height="615" alt="image" src="https://github.com/user-attachments/assets/cefc193e-f34c-4656-9e6d-447702eca19d" />

---

## Step 4: Run Container

```bash
docker run my-ubuntu:v1
```

Output:

<img width="634" height="58" alt="image" src="https://github.com/user-attachments/assets/b69cc00d-c86b-4fd6-9ed1-a80036eb14dd" />

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
<img width="1159" height="613" alt="image" src="https://github.com/user-attachments/assets/1d674c88-3b96-4f49-a2f2-75134763d35d" />

---

## Run

```bash
docker run -d -p 8080:80 nginx-demo:v1
```

Visit:

```
http://localhost:8080
```
<img width="989" height="424" alt="image" src="https://github.com/user-attachments/assets/9afb7191-7c1b-4378-86c5-6a735dca8a09" />

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
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Docker Deployment | Shibnath Das</title>

  <style>
    body {
      margin: 0;
      font-family: "Segoe UI", Roboto, Arial, sans-serif;
      background: linear-gradient(135deg, #0f2027, #203a43, #2c5364);
      color: #ffffff;
      display: flex;
      justify-content: center;
      align-items: center;
      height: 100vh;
    }

    .container {
      background: rgba(255, 255, 255, 0.08);
      backdrop-filter: blur(10px);
      padding: 40px 60px;
      border-radius: 12px;
      text-align: center;
      box-shadow: 0 20px 40px rgba(0, 0, 0, 0.4);
      max-width: 700px;
    }

    h1 {
      font-size: 2.5rem;
      margin-bottom: 10px;
      color: #00d4ff;
    }

    h2 {
      font-weight: 400;
      margin-bottom: 30px;
      color: #e0e0e0;
    }

    .badge {
      display: inline-block;
      padding: 10px 18px;
      margin: 5px;
      border-radius: 25px;
      background: #00d4ff;
      color: #000;
      font-weight: 600;
      font-size: 0.9rem;
    }

    .section {
      margin-top: 30px;
      text-align: left;
    }

    .section h3 {
      border-left: 4px solid #00d4ff;
      padding-left: 10px;
      margin-bottom: 15px;
      color: #ffffff;
    }

    ul {
      list-style: none;
      padding: 0;
    }

    ul li {
      padding: 6px 0;
      font-size: 0.95rem;
      color: #e6e6e6;
    }

    .footer {
      margin-top: 30px;
      font-size: 0.85rem;
      color: #cccccc;
    }

    a {
      color: #00d4ff;
      text-decoration: none;
    }

    a:hover {
      text-decoration: underline;
    }
  </style>
</head>

<body>
  <div class="container">
    <h1>Dockerized Web Application</h1>
    <h2>Built & Deployed by Shibnath Das</h2>

    <div>
      <span class="badge">Docker</span>
      <span class="badge">Nginx</span>
      <span class="badge">Linux</span>
      <span class="badge">DevOps</span>
    </div>

    <div class="section">
      <h3>🚀 What This Demonstrates</h3>
      <ul>
        <li>✔ Custom Dockerfile creation</li>
        <li>✔ Image build & tagging</li>
        <li>✔ Containerized Nginx deployment</li>
        <li>✔ Port mapping and runtime execution</li>
      </ul>
    </div>

    <div class="section">
      <h3>📂 Project Info</h3>
      <ul>
        <li>Image: <strong>my-website:v1</strong></li>
        <li>Base Image: <strong>nginx:alpine</strong></li>
        <li>Exposed Port: <strong>80</strong></li>
      </ul>
    </div>

    <div class="footer">
      Part of my 90 Days of DevOps journey.<br>
      Connect with me on 
      <a href="https://www.linkedin.com/in/shibnath/" target="_blank">LinkedIn</a>
    </div>
  </div>
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

<img width="1358" height="684" alt="image" src="https://github.com/user-attachments/assets/b972daf5-2cc8-4427-8a51-73fc6b86399f" />


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

