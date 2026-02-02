# Docker Learning Guide (Digital Transformation Team)

A practical Docker learning path focused on building, running, and shipping services reliably.

---

## 1) Outcome (What “done” looks like)
In **2–3 weeks**, each engineer can:
- Containerize a small app (web/API) with a clean **Dockerfile**
- Run multi-service stacks with **Docker Compose** (app + DB + cache)
- Use **volumes**, **networks**, **env vars**, and **healthchecks** correctly
- Debug container issues (logs, exec, inspect)
- Produce images for **CI/CD** and deploy safely (dev/stage/prod differences)
- Apply basic **security** and best practices (non-root, minimal images, secrets)

---

## 2) Phase 0 — Setup (Day 0)

### Install
- Docker Desktop (Mac/Windows) or Docker Engine (Linux)
- VS Code + Docker extension (optional)

### Verify
- `docker version`
- `docker run --rm hello-world`

### 15-minute concept map
- **Image** vs **Container**
- **Registry** (Docker Hub / ECR / GCR / ACR)
- **Dockerfile** builds images
- **Compose** orchestrates local multi-container dev

---

## 3) Phase 1 — Docker Fundamentals (Days 1–3)

### Must-know commands
**Images/containers**
- `docker images`
- `docker ps -a`
- `docker run ...`
- `docker stop <id>` / `docker rm <id>`

**Inspect/debug**
- `docker logs -f <id>`
- `docker exec -it <id> sh` (or `bash`)
- `docker inspect <id>`

**Cleanup**
- `docker system df`
- `docker system prune` *(careful; removes unused resources)*

### Lab 1 (60–90 mins): run a web server + customize it
**Task:** run nginx and serve a custom `index.html`.

1) Run nginx
- `docker run --rm -p 8080:80 nginx`

2) Mount current folder into the container
- `docker run --rm -p 8080:80 -v "$PWD":/usr/share/nginx/html:ro nginx`

**Skills learned**
- Port mapping (`-p`)
- Bind mounts (`-v`)
- Container lifecycle (`--rm`)

---

## 4) Phase 2 — Building Images (Days 4–7)

### Dockerfile building blocks
Learn these in order:
- `FROM`
- `WORKDIR`
- `COPY`
- `RUN`
- `ENV`
- `EXPOSE` *(documentation only)*
- `CMD` vs `ENTRYPOINT`

### Lab 2: containerize a simple API (choose your stack)
Recommended repo structure:
- `app/`
  - source code
  - `Dockerfile`
  - `.dockerignore`

**Requirements**
- App runs with `docker build` + `docker run`
- Uses **environment variables** for config
- Reproducible builds (no “works on my machine”)

### Best practices (DX-friendly)
- Use minimal base images (e.g., `python:3.12-slim`, `node:20-alpine` *with care*)
- Add `.dockerignore` (avoid copying `node_modules`, build output, secrets)
- Pin versions where it matters
- Use **multi-stage builds** for compiled apps
- Run as **non-root** user when possible
- Add a **HEALTHCHECK** for long-running services

---

## 5) Phase 3 — Docker Compose for Real Projects (Week 2)

### Core concepts
- **Services**
- **Networks** (service discovery by name)
- **Volumes** (persistent DB data)
- **Environment variables** / `.env`
- Dependencies and **healthchecks**

### Lab 3: App + Postgres
**Task:** create a `compose.yml` with:
- `app` service (built from your Dockerfile)
- `db` service (postgres image)
- a **named volume** for Postgres data
- optional migrations/seed

**Commands**
- `docker compose up -d`
- `docker compose logs -f`
- `docker compose exec app sh`
- `docker compose down` (explain `-v` for removing volumes)

### Common team patterns
- Separate dev vs prod compose files:
  - `compose.yml` (base)
  - `compose.override.yml` (dev mounts, debug)
- Use **profiles** for optional services (e.g., `redis`, `mailhog`)
- Never bake secrets into images (use env vars now; secret manager later)

---

## 6) Phase 4 — Shipping: Registries + CI/CD (Week 3)

### Tagging strategy
- `myapp:<git-sha>`
- `myapp:<semver>`
- `myapp:latest` *(optional; avoid for prod)*

### Registry workflow
- `docker login`
- `docker tag ...`
- `docker push ...`

### CI/CD (high-level)
Typical pipeline:
- Build image
- Run tests inside container
- Scan image (optional but recommended)
- Push to registry
- Deploy (Kubernetes / ECS / VM compose / etc.)

---

## 7) Phase 5 — Operations & Troubleshooting (Ongoing)

### Debug checklist (muscle memory)
- Is the container running? `docker ps -a`
- Why did it exit? `docker logs <id>`
- What command is it running?
  - `docker inspect --format '{{.Config.Cmd}}' <id>`
- Is the port mapped? `docker port <id>`
- In Compose: is DNS/service name resolving?
- File permissions / user issues?
- Environment variables correct?

---

## 8) Recommended resources
- Docker docs: Get Started + Dockerfile reference
- Play With Docker (browser sandbox) for quick demos
- Internal repo: 3 example services with Dockerfiles + Compose

---

## 9) Team deliverables (to make it stick)
1) **Internal “Docker Standards”**
- approved base images
- tagging rules
- non-root policy
- compose conventions
- logging conventions

2) **Reference templates**
- Node/Python/Java Dockerfile templates
- Compose template for app+db
- CI pipeline snippet

3) **Capstone**
Each person containerizes one real service from the DX backlog, adds Compose, and documents run/debug steps.
