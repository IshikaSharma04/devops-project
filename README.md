# OpsGrid

OpsGrid is an incident response and service health tracking platform built for site reliability and cloud engineering teams. It provides real-time service health checks, incident lifecycle management, and Prometheus metrics out of the box.

---

## Features

- **Incident Triage & Tracking:** Declare, investigate, resolve, and archive incidents with severity levels (`LOW`, `MEDIUM`, `HIGH`, `CRITICAL`).
- **Service Catalog Monitoring:** Monitor latency and uptime percentages across core infrastructure services.
- **Health & Readiness Probes:** Live database connectivity check via `/health`.
- **Prometheus Metrics:** Standard `/metrics` endpoint instrumented for request throughput, latency histograms, and error rates.
- **Containerized Stack:** Multi-stage, unprivileged non-root Docker builds for frontend and backend.
- **Production-Ready Deployments:** Helm charts with Horizontal Pod Autoscaling (HPA) and Kubernetes manifests.
- **Infrastructure as Code:** Terraform configuration for an AWS VPC and managed EKS cluster.
- **Automated CI/CD:** GitHub Actions pipeline with unit tests, Trivy vulnerability scanning, and Docker builds.

---

## Architecture

```mermaid
graph TD
    Client["Client Browser"] -->|HTTP :3000| Ingress["Ingress / Reverse Proxy"]
    
    subgraph Cluster ["Kubernetes / Docker Network"]
        Ingress -->|/ | Frontend["React Frontend (Nginx)"]
        Ingress -->|/api, /health, /metrics | Backend["FastAPI Backend (Python)"]
        
        Backend -->|SQLAlchemy| Postgres[("PostgreSQL Database")]
        Backend -->|Exposes :8000/metrics| Prometheus["Prometheus Server"]
    end

    Prometheus --> Grafana["Grafana Dashboards"]
```

---

## Tech Stack

- **Backend:** FastAPI, Python 3.11, SQLAlchemy, Pydantic, Alembic, Uvicorn
- **Frontend:** React 18, Vite, Vanilla CSS
- **Database:** PostgreSQL 16
- **Testing:** Pytest, FastAPI TestClient
- **Containers & Orchestration:** Docker, Docker Compose, Kubernetes, Helm
- **Infrastructure as Code:** Terraform (AWS VPC, Subnets, EKS)
- **CI/CD & Security:** GitHub Actions, Trivy
- **Observability:** Prometheus, Grafana

---

## Quickstart (Local Docker Compose)

The easiest way to run the full stack locally is with Docker Compose:

```bash
git clone https://github.com/IshikaSharma04/devops-project.git
cd devops-proj

docker compose up -d --build

docker compose ps
```

Once running, access the services:
- **Web UI:** http://localhost:3000
- **API Documentation (Swagger):** http://localhost:8000/docs
- **Health Check:** http://localhost:8000/health
- **Prometheus Metrics:** http://localhost:8000/metrics

To shut down:
```bash
docker compose down
```

---

## Running Tests

Automated tests run against an isolated in-memory SQLite database so your local database is untouched:

```bash
python3 -m venv .venv
source .venv/bin/activate
pip install -r backend/requirements.txt

PYTHONPATH=. pytest backend/tests -v
```

---

## API Reference

| Method | Endpoint | Description |
|---|---|---|
| `GET` | `/health` | Service and database health check |
| `GET` | `/metrics` | Prometheus observability metrics |
| `GET` | `/api/v1/incidents` | List incidents (supports `?status=` and `?severity=` filters) |
| `POST` | `/api/v1/incidents` | Create a new incident |
| `GET` | `/api/v1/incidents/{id}` | Get incident details |
| `PUT` | `/api/v1/incidents/{id}` | Update incident status or details |
| `DELETE` | `/api/v1/incidents/{id}` | Delete / archive an incident |
| `GET` | `/api/v1/services` | Service catalog and uptime status |
| `GET` | `/api/v1/stats` | Summary statistics of active incidents |

---

## Kubernetes & Helm

Deploy the stack to a local or remote Kubernetes cluster (e.g. Minikube or Kind):

```bash
kubectl apply -f k8s/namespace.yaml

helm upgrade --install cloudpulse ./helm --namespace cloudpulse

kubectl get pods -n cloudpulse
```

---

## Terraform (AWS Infrastructure)

Terraform configurations for provisioning an AWS VPC and EKS cluster reside in `terraform/`:

```bash
cd terraform

terraform init

terraform validate

terraform plan -var-file=terraform.tfvars.example
```

---

## CI/CD Pipeline

The `.github/workflows/ci-cd.yml` workflow runs automatically on every push to `main`:
1. **Test:** Runs Pytest test suite with code quality checks.
2. **Build:** Runs frontend compilation (`npm run build`).
3. **Security:** Runs Trivy vulnerability scanner to flag high/critical CVEs in container images.
4. **Publish:** Builds and tags Docker images for deployment.

dashboard :
<img width="2844" height="1616" alt="image" src="https://github.com/user-attachments/assets/b35d1d6a-8181-4eed-b6ff-d8f2eb6652cf" />

dockercompose :
<img width="1906" height="302" alt="image" src="https://github.com/user-attachments/assets/cfad3c8e-8f4a-4c71-8bfa-d8aa9de3066e" />

pytest :
<img width="1530" height="616" alt="image" src="https://github.com/user-attachments/assets/0bc4345b-5ec3-4e6d-bb74-83deeba0266d" />
<img width="1720" height="230" alt="image" src="https://github.com/user-attachments/assets/9fc40ac3-7243-46a0-a938-3fc8dbff7bb8" />

githubrepo :
<img width="1514" height="1372" alt="image" src="https://github.com/user-attachments/assets/6f2148c5-aa3c-45a7-89c6-1038fe0beba4" />

pipeline :
<img width="1796" height="692" alt="image" src="https://github.com/user-attachments/assets/57d8ac14-02e0-47e9-baf1-95528f89a4eb" />
<img width="1640" height="584" alt="image" src="https://github.com/user-attachments/assets/e859d930-745b-4c0d-8df9-02b3f571bde5" />

packages :
<img width="1758" height="572" alt="image" src="https://github.com/user-attachments/assets/938dc543-3cca-4e14-824a-fed61ed00a94" />

trivyscan:
<img width="1410" height="1086" alt="image" src="https://github.com/user-attachments/assets/1a711b9f-5bc3-4dca-84bd-3b43b144aef7" />

prmetheus metrics :
<img width="2492" height="1681" alt="image" src="https://github.com/user-attachments/assets/dc8410cc-1302-41d9-955c-66811479387a" />

demo video :
https://screenrec.com/share/e9YhKQrWFP

**Presentation:**

[View Project Presentation (PPT)](./OpsGrid_Cream_Pink_Presentation.pptx)


