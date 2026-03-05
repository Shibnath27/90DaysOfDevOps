# day-38-yaml.md

## Day 38 – YAML Basics

Today I learned the fundamentals of **YAML**, which is widely used in:

* CI/CD pipelines
* Docker Compose
* Kubernetes manifests
* GitHub Actions / GitLab CI
* Infrastructure as Code tools

YAML stands for **“YAML Ain't Markup Language.”**
It is a **human-readable data serialization format** that relies heavily on **indentation**.

---

# Core YAML Rules

1. **Spaces only — never tabs**
2. **Indentation defines structure**
3. Standard indentation = **2 spaces**
4. Keys and values use **key: value**
5. Lists use **- item**
6. Booleans: `true` / `false`
7. Strings usually don’t need quotes

Example:

```yaml
name: Shibnath
role: DevOps Engineer
learning: true
```

---

# Task 1 – Key Value Pairs

File: **person.yaml**

```yaml
name: Shibnath Das
role: DevOps Engineer
experience_years: 3
learning: true
```

Notes:

* `learning: true` is a boolean
* `"true"` would be treated as a **string**

---

# Task 2 – Lists

Updated **person.yaml**

```yaml
name: Shibnath Das
role: DevOps Engineer
experience_years: 3
learning: true

tools:
  - docker
  - kubernetes
  - git
  - ansible
  - terraform

hobbies: [reading, tech-experiments, gaming]
```

### Two ways to write lists in YAML

**Block style**

```yaml
tools:
  - docker
  - kubernetes
  - git
```

**Inline style**

```yaml
tools: [docker, kubernetes, git]
```

---

# Task 3 – Nested Objects

File: **server.yaml**

```yaml
server:
  name: web-server-01
  ip: 192.168.1.10
  port: 8080

database:
  host: db.internal
  name: appdb
  credentials:
    user: admin
    password: strongpassword
```

Key learning:

Indentation creates **hierarchy**.

```
database
 └── credentials
      ├── user
      └── password
```

---

# Task 4 – Multi-line Strings

Updated **server.yaml**

```yaml
startup_script_pipe: |
  #!/bin/bash
  echo "Starting application"
  systemctl start nginx
  systemctl start docker

startup_script_fold: >
  This script starts the
  required services
  for the application server.
```

### Difference

`|` **Literal block**

* Preserves new lines
* Used for **scripts, configs, certificates**

Example result:

```
line1
line2
line3
```

`>` **Folded block**

* Converts new lines into spaces
* Used for **long text descriptions**

Example result:

```
line1 line2 line3
```

---

# Task 5 – YAML Validation

Tools used:

* `yamllint`
* online validator (yamllint.com)

Command:

```bash
yamllint person.yaml
yamllint server.yaml
```

Common error when breaking indentation:

Example mistake:

```yaml
tools:
 - docker
  - kubernetes
```

Error:

```
syntax error: mapping values are not allowed here
```

Fix by using consistent **2-space indentation**.

---

# Task 6 – Spot the Difference

### Correct

```yaml
name: devops
tools:
  - docker
  - kubernetes
```

### Broken

```yaml
name: devops
tools:
- docker
  - kubernetes
```

### What is wrong?

The list items are **not aligned properly**.

Correct indentation:

```
tools:
  - docker
  - kubernetes
```

Broken indentation:

```
tools:
- docker
  - kubernetes
```

YAML parser cannot determine the structure.

