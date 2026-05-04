# 🐳 Docker — Dockerfiles, Compose & Best Practices

[![Docker](https://img.shields.io/badge/Docker-24%2B-2496ED?style=flat-square&logo=docker)](https://www.docker.com/)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg?style=flat-square)](LICENSE)

Production-ready Dockerfiles and docker-compose configurations. Demonstrates multi-stage builds, minimal base images, security hardening, and layer caching optimisation for faster CI/CD pipelines.

---

## 📁 Repository Structure

```
docker/
├── nodejs/
│   ├── Dockerfile              # Multi-stage Node.js production image
│   └── .dockerignore
├── python-flask/
│   ├── Dockerfile              # Multi-stage Python/Flask image
│   └── .dockerignore
├── nginx-proxy/
│   ├── Dockerfile              # Custom Nginx reverse proxy
│   └── nginx.conf
├── compose/
│   ├── docker-compose.yml      # Full stack: app + nginx + postgres + redis
│   └── .env.example
└── README.md
```

---

## 🚀 Quick Start

```bash
# Build and run the Node.js app
docker build -t nodejs-app:latest ./nodejs
docker run -p 3000:3000 nodejs-app:latest

# Run the full stack with docker-compose
cp compose/.env.example compose/.env
docker compose -f compose/docker-compose.yml up -d

# Check running containers
docker compose -f compose/docker-compose.yml ps
```

---

## 🏗️ Key Techniques

### Multi-Stage Builds
- **Builder stage:** installs all dev dependencies and builds the app
- **Production stage:** copies only the compiled output from the builder
- Result: **image size reduced by 60-70%**

### Security Hardening
- Non-root user (`node`, `appuser`) runs the application process
- `--no-cache` for package installations (no stale cache in image)
- Read-only root filesystem where possible
- No SSH, no shell tools in production images

### Layer Caching Optimisation
- `package.json` / `requirements.txt` copied first, then `npm ci` / `pip install`
- Application source code copied last
- Result: dependency install layer is cached on every build unless deps change

---

## 🔒 Security Checklist

- ✅ Non-root user in every production Dockerfile
- ✅ Minimal base images (`alpine`, `slim`, `distroless`)
- ✅ Multi-stage builds to exclude dev tools from final image
- ✅ `.dockerignore` excludes `.git`, `node_modules`, secrets
- ✅ No hardcoded secrets — environment variables only
- ✅ `HEALTHCHECK` instruction in every Dockerfile

---

## 📋 Related Repos

- [jenkins-k8s-cicd-pipeline](https://github.com/shivam74826/jenkins-k8s-cicd-pipeline) — Jenkins builds these images in CI
- [Kubernetes](https://github.com/shivam74826/Kubernetes) — These images are deployed to K8s

---

*Part of my [DevOps Portfolio](https://github.com/shivam74826)*
