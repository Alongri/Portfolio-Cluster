# 🚀 Portfolio Cluster Helm Charts

Welcome to the **Portfolio Cluster** repository! This repo contains Helm charts for deploying your **Flask application** and **MongoDB** on a Kubernetes cluster (e.g., AWS EKS), along with NGINX ingress configuration.

---

## 📁 Repository Structure

```text
Portfolio-Cluster/
├── README.md
└── charts
    ├── flask-app         # Helm chart for the Flask application
    │   ├── Chart.yaml
    │   ├── templates
    │   │   ├── deployment.yaml
    │   │   ├── ingress.yaml
    │   │   └── service.yaml
    │   └── values.yaml
    └── mongodb-chart     # Helm chart for MongoDB
        ├── Chart.yaml
        ├── charts        # (optional: subcharts)
        └── values.yaml
```

* **flask-app**: Contains templates and configuration for deploying the Flask app.
* **mongodb-chart**: Contains templates and configuration for deploying MongoDB with persistence and authentication.

---

## 🎯 Goals

* Deploy **Flask web application** with environment variables configured for MongoDB.
* Deploy **MongoDB** with secure credentials and persistent storage.
* Use **NGINX Ingress** to expose the Flask app to the public internet.
* Support **umbrella chart** architecture for easy deployment of both apps together.

---

## ⚙️ Helm Charts

### 1️⃣ Flask App (`flask-app`)

* **Docker Image**: Configurable in `values.yaml`
* **Replicas**: Set using `replicaCount`
* **Environment Variables**:

```yaml
env:
  MONGO_HOST: <mongodb service name>
  MONGO_PORT: 27017
  MONGO_DB: flaskdb
```

* **Ingress**: Public domain setup:

```yaml
ingress:
  enabled: true
  hosts:
    - host: myportfolio.dbloader.com
      paths:
        - path: /
```

* **Probes**: Liveness & Readiness configured for `/health` endpoint
* **Resources**: CPU & memory can be configured in `values.yaml`

---

### 2️⃣ MongoDB (`mongodb-chart`)

* **Authentication**:

```yaml
auth:
  rootPassword: "YourRootPassword"
  username: "appuser"
  password: "admin123"
  database: "flaskdb"
```

* **Persistence**:

```yaml
persistence:
  storageClass: "ebs-csi-gp3"
```

* Deploys a **single MongoDB instance** by default (can be scaled for production).

---

## 📦 Umbrella Chart

* Allows you to deploy both **Flask app** and **MongoDB** together.
* Dependencies are defined in `Chart.yaml` of umbrella chart:

```yaml
dependencies:
  - name: mongodb-chart
    version: 0.1.0
    repository: "file://../mongodb-chart"
  - name: flask-app
    version: 0.1.0
    repository: "file://../flask-app"
```

* **Install or upgrade** umbrella chart:

```bash
helm dependency update
helm install portfolio ./portfolio-umbrella -n app --create-namespace
# or upgrade
helm upgrade portfolio ./portfolio-umbrella -n app
```

---

## 📝 Notes / Best Practices

* ✅ Use **numeric tags** for Docker images instead of `latest` for immutable deployments.
* ✅ Set `MONGO_HOST` in Flask app to match MongoDB service name in cluster.
* ✅ Use proper DNS for ingress hosts (`myportfolio.dbloader.com`) pointing to LoadBalancer.
* ✅ Define CPU/memory **resources** in `values.yaml` for production.
* ✅ Keep sensitive credentials in **Kubernetes Secrets** or use Helm secrets plugin.

---

## 🌐 NGINX Ingress

* The Flask app is exposed via **Ingress** controller.
* Ensure your **DNS** points to the LoadBalancer created by the ingress controller.
* Example:

```text
myportfolio.dbloader.com → <Ingress LoadBalancer DNS>
```

---

## 🧩 Summary

This repo allows you to:

* Deploy a **scalable Flask app**
* Deploy a **secure MongoDB** instance
* Use **NGINX Ingress** for public access
* Manage everything through **Helm charts** in an **umbrella structure**

---

## 🚀 Quick Start

1. Update your Docker images in `values.yaml`.
2. Update MongoDB credentials.
3. Set your ingress host (DNS).
4. Run:

```bash
helm dependency update
helm install portfolio ./portfolio-umbrella -n app --create-namespace
```

5. Access your Flask app via the domain: `https://myportfolio.dbloader.com`

---

✨ Enjoy your fully automated Kubernetes deployment!


