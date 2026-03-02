# 📄 `day-37-revision.md`

# Day 37 – Docker Revision & Self-Assessment

---

## 🔹 Self-Assessment Checklist



✔ Run container (interactive + detached) — **Can Do**

✔ Manage containers/images — **Can Do**

✔ Explain image layers & caching — **Can Do**

✔ Write Dockerfile from scratch — **Can Do**

✔ CMD vs ENTRYPOINT — **Can Do**

✔ Build & tag image — **Can Do**

✔ Named volumes — **Can Do**

✔ Bind mounts — **Can Do**

✔ Custom networks — **Can Do**

✔ Docker Compose multi-container — **Can Do**

✔ Environment variables in Compose — **Can Do**

✔ Multi-stage builds — **Can Do**

✔ Push to Docker Hub — **Can Do**

✔ Healthchecks & depends_on — **Can Do**


---

## 🔹 Quick-Fire Answers

**1. Image vs Container**
Image = Immutable template.
Container = Running instance of an image.

---

**2. What happens to container data when removed?**
It is lost unless stored in a volume or bind mount.

---

**3. How do containers on same custom network communicate?**
Via embedded Docker DNS using service/container names.

---

**4. docker compose down -v vs docker compose down**
`down` → Removes containers & networks
`down -v` → Also removes named volumes

---

**5. Why multi-stage builds?**
Smaller images, reduced attack surface, faster deployment.

---

**6. COPY vs ADD**
COPY = Simple file copy (preferred).
ADD = Supports URL and auto-extract tar (less predictable).

---

**7. -p 8080:80 means?**
Host port 8080 maps to container port 80.

---

**8. Check Docker disk usage?**

```
docker system df
```

---

# 🔹 Key Concepts That Must Stick

* Containers are ephemeral
* Volumes provide persistence
* Custom networks provide DNS resolution
* Compose makes infra declarative
* Multi-stage builds optimize production images
* Never use `latest` blindly
* Don’t run containers as root
* Healthchecks prevent race conditions

---

# 🔹 Reflection

Days 29–36 covered:

* Container lifecycle
* Dockerfile fundamentals
* Image optimization
* Volumes & networking
* Compose orchestration
* Production-style stacks
* Docker Hub distribution

At this point, Docker fundamentals are solid.

The next evolution is orchestration at scale.

