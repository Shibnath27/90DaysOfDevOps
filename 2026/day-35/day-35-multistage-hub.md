# 📄 `day-35-multistage-hub.md`

---

# Day 35 – Multi-Stage Builds & Docker Hub

Today’s focus was image optimization and distribution — two topics frequently discussed in DevOps interviews.

---

# 🔹 Task 1: The Problem with Large Images

I created a simple Node.js application.

---

## 📁 Project Structure

```
day-35/
│
├── app.js
├── package.json
└── Dockerfile.single
```

---

## 🔹 app.js

```javascript
const http = require("http");

const server = http.createServer((req, res) => {
  res.end("Hello from Node.js Multi-Stage Demo!");
});

server.listen(3000, () => {
  console.log("Server running on port 3000");
});
```

---

## 🔹 package.json

```json
{
  "name": "multistage-demo",
  "version": "1.0.0",
  "main": "app.js",
  "dependencies": {}
}
```

---

## 🔹 Single-Stage Dockerfile (Dockerfile.single)

```dockerfile
FROM node:18

WORKDIR /app

COPY package.json .
RUN npm install

COPY . .

EXPOSE 3000

CMD ["node", "app.js"]
```

---

## 🔹 Build Image

```bash
docker build -f Dockerfile.single -t node-single .
```

Check size:

```bash
docker images
```

Example size:
~ 900MB (node full Debian-based image)

---

# 🔹 Task 2: Multi-Stage Build

Now optimized using multi-stage.

---

## 🔹 Multi-Stage Dockerfile (Dockerfile)

```dockerfile
# Stage 1: Builder
FROM node:18-alpine AS builder

WORKDIR /app

COPY package.json .
RUN npm install --production

COPY . .

# Stage 2: Minimal Runtime
FROM node:18-alpine

WORKDIR /app

COPY --from=builder /app /app

RUN addgroup -S appgroup && adduser -S appuser -G appgroup
USER appuser

EXPOSE 3000

CMD ["node", "app.js"]
```

---

## 🔹 Build Optimized Image

```bash
docker build -t node-multistage .
```

Check size:

```bash
docker images
```

Example size:
~ 120–150MB

---

## 🔹 Comparison

| Image Type             | Approx Size |
| ---------------------- | ----------- |
| Single Stage (node:18) | ~900MB      |
| Multi-Stage (alpine)   | ~130MB      |

---

## 🔹 Why Multi-Stage is Smaller?

* No build dependencies in final image
* Alpine base instead of full Debian
* Reduced layers
* Production-only dependencies
* No unnecessary package managers or tooling

This reduces:

* Attack surface
* Storage usage
* Pull time
* Deployment time

---

# 🔹 Task 3: Push to Docker Hub

---

## 🔹 Login

```bash
docker login
```

---

## 🔹 Tag Image

```bash
docker tag node-multistage yourusername/node-multistage:1.0
```

---

## 🔹 Push

```bash
docker push yourusername/node-multistage:1.0
```

---

## 🔹 Verify

Remove local image:

```bash
docker rmi yourusername/node-multistage:1.0
```

Pull again:

```bash
docker pull yourusername/node-multistage:1.0
```

Image pulls successfully from Docker Hub.

---

# 🔹 Task 4: Docker Hub Repository

On Docker Hub:

* Verified image exists
* Added repository description
* Explored tags tab

Example:

* yourusername/node-multistage:1.0
* yourusername/node-multistage:latest

Pull specific tag:

```bash
docker pull yourusername/node-multistage:1.0
```

Pull latest:

```bash
docker pull yourusername/node-multistage:latest
```

Using specific tags ensures predictable deployments.

Using `latest` may cause unintended version changes.

---

# 🔹 Task 5: Image Best Practices Applied

---

### ✅ Minimal Base Image

Compared:

* node:18 (Debian-based)
* node:18-alpine

Alpine significantly smaller.

---

### ✅ Non-Root User

```dockerfile
RUN addgroup -S appgroup && adduser -S appuser -G appgroup
USER appuser
```

Improves security posture.

---

### ✅ Combine RUN Commands

Reduces layers and image size.

---

### ✅ Avoid `latest`

Used:

```dockerfile
FROM node:18-alpine
```

instead of:

```
FROM node:latest
```

Ensures version consistency.

---

# 🔥 Key Learnings

* Multi-stage builds drastically reduce image size.
* Smaller images improve security and deployment speed.
* Never ship build tools in production image.
* Use non-root users for security.
* Always tag images explicitly.
* Docker Hub enables distribution and version control.

