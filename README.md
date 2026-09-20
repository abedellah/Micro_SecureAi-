<img width="996" height="1280" alt="WhatsApp Image 2026-06-23 at 11 47 47" src="https://github.com/user-attachments/assets/43302906-8552-4980-80cf-3e145ab9bb37" />

# SecureAI MicroShield

An AI-powered cybersecurity microservices platform deployed on Kubernetes. The system detects anomalous API traffic patterns using Isolation Forest machine learning models and surfaces automated alerts through a real-time Security Operations Center (SOC) dashboard.

---

## Architecture

| Service | Technology | Port | Purpose |
|---|---|---|---|
| Frontend | React 18 + Vite | 5173 | SOC Dashboard UI and OIDC authentication handling |
| Backend | Django 6.0 | 8000 | REST API, audit logging, and alert management |
| AI Service | FastAPI + scikit-learn | 5000 | Anomaly detection via Isolation Forest |
| Keycloak | Keycloak 24 | 8080 | Identity and access management (OIDC) |
| PostgreSQL | PostgreSQL 15 | 5432 | Primary relational database |
| Redis | Redis 7 | 6379 | High-speed security cache and rate-limiting |
| Prometheus | Prometheus | 9090 | Systems and security metrics collection |
| Grafana | Grafana | 3000 | Visual SOC dashboards |
| Loki + Promtail | Grafana Loki | 3100 | Distributed log aggregation |

---

## Quick Start

### 1. Infrastructure Setup

```bash
# Start Minikube
minikube start --driver=docker --memory=4096 --cpus=2

# Enable NGINX Ingress
minikube addons enable ingress
```

### 2. Build and Load Images

```bash
# Build service images
docker build -t backend-backend:latest ./backend
docker build -t ai-service:latest ./backend/ai_service
docker build -t frontend-ui:latest ./frontend

# Load images into the Minikube internal registry
& 'C:\Program Files\Kubernetes\Minikube\minikube.exe' image load backend-backend:latest
& 'C:\Program Files\Kubernetes\Minikube\minikube.exe' image load ai-service:latest
& 'C:\Program Files\Kubernetes\Minikube\minikube.exe' image load frontend-ui:latest
```

### 3. Deploy to Kubernetes

```bash
# Apply all manifests in dependency order
kubectl apply -f k8s/database/
kubectl apply -f k8s/identity/
kubectl apply -f k8s/apps/
kubectl apply -f k8s/monitoring/
kubectl apply -f k8s/base/

# Expose the cluster via Ingress (Windows)
minikube tunnel
```

> Add the following entry to `C:\Windows\System32\drivers\etc\hosts` if not already present:
> ```
> 127.0.0.1 secureai.local
> ```

---

## Security Features

**Zero-Trust Identity**
All access is governed by RS256-signed JWT tokens issued through Keycloak via OpenID Connect. No credentials are stored locally.

**Behavioral AI Detection**
An Isolation Forest microservice performs real-time outlier detection, analyzing request frequency, payload size, and latency to identify zero-day threats before signatures exist.

**Full-Stack Observability**
An integrated PLG stack (Prometheus, Loki, Grafana) provides forensic traceability from network-level metrics down to individual container logs.

**Container Hardening**
All deployments enforce non-root execution, dropped Linux capabilities, and read-only filesystems wherever the runtime permits.

---

## DevSecOps Pipeline

Every push to `main` triggers an automated four-gate security validation pipeline. The pipeline fails immediately on any HIGH or CRITICAL finding.

| Gate | Tool | Scope |
|---|---|---|
| SAST | Bandit | Static analysis of Python source code for security vulnerabilities |
| SCA | pip-audit | Known CVEs in Python and frontend dependencies |
| IaC | Checkov | Security context and best practices across Kubernetes manifests |
| Container | Trivy | OS-level vulnerabilities within built Docker images |

---

## Dependency Vulnerability Management

**Remediation Policy**
High and Critical severity findings must be resolved by version upgrade within 48 hours of detection.

**Frontend Auditing**
Node.js dependencies are audited for supply-chain vulnerabilities using both `npm audit` and Trivy filesystem scans.

**Base Image Hardening**
All microservices are built on `python:3.12-slim-bookworm` or `node:20-slim` to minimize the OS-level attack surface.

**Exception Handling**
Unfixable upstream OS vulnerabilities are documented in `.trivyignore` with full technical justification and are subject to monthly review.

---

## Running Security Scans Locally

```bash
# Python static analysis
bandit -r backend/ -x backend/venv --severity-level medium

# Dependency audit (Backend and AI Service)
pip-audit -r backend/requirements.txt
pip-audit -r backend/ai_service/requirements.txt

# Kubernetes manifest validation
checkov -d k8s/ --framework kubernetes --compact

# Container vulnerability scan
trivy image --severity HIGH,CRITICAL backend-backend:latest
trivy image --severity HIGH,CRITICAL frontend-ui:latest
```

---

## Contributors

Team end-of-studies project, built jointly by:

- **Yassir Nmar** ([@ChiefYasser](https://github.com/ChiefYasser)) — project owner
- **Mohamed Abdellah Lagrini** — contributed across the stack (backend, frontend, and DevSecOps/infrastructure)

*SecureAI MicroShield — End-of-Studies Project, 2026*
