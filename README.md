# Phishing Directory — Kubernetes Deployment Project

A containerized web application deployed on a local Kubernetes cluster using **Minikube**. The project demonstrates containerization, Kubernetes deployments and services, persistent storage, RBAC, Ingress routing, application-to-database communication, and Git-based project management.

> **Educational / Lab Project:** This project was created for Kubernetes and DevOps learning purposes. The frontend is a simulated login interface and should only be used with dummy test credentials in an isolated lab environment.

---

## 📌 Project Overview

The project consists of three main application components:

- **Frontend** — NGINX serving a custom HTML login interface.
- **Backend** — Python Flask application responsible for receiving form submissions and storing them in MySQL.
- **MySQL Database** — Persistent database used to store the submitted test data.

The complete application was containerized with Docker and deployed to Kubernetes using Minikube.

The project also implements Kubernetes **RBAC**, **Persistent Volumes**, **Persistent Volume Claims**, **ClusterIP Services**, and an **NGINX Ingress Controller**.

---

## 🏗️ Architecture

```text
                         ┌──────────────────────┐
                         │       Browser        │
                         │  phishing.local      │
                         └──────────┬───────────┘
                                    │
                                    ▼
                         ┌──────────────────────┐
                         │    NGINX Ingress     │
                         │      Controller      │
                         └──────────┬───────────┘
                                    │
                                    ▼
                         ┌──────────────────────┐
                         │  Frontend Service    │
                         │      ClusterIP       │
                         └──────────┬───────────┘
                                    │
                                    ▼
                         ┌──────────────────────┐
                         │  Frontend Pod        │
                         │       NGINX          │
                         └──────────┬───────────┘
                                    │
                              /submit
                                    │
                                    ▼
                         ┌──────────────────────┐
                         │   Backend Service    │
                         │      ClusterIP       │
                         └──────────┬───────────┘
                                    │
                                    ▼
                         ┌──────────────────────┐
                         │   Backend Pod        │
                         │   Python / Flask     │
                         └──────────┬───────────┘
                                    │
                              mysql-service
                                    │
                                    ▼
                         ┌──────────────────────┐
                         │   MySQL Service      │
                         │      ClusterIP       │
                         └──────────┬───────────┘
                                    │
                                    ▼
                         ┌──────────────────────┐
                         │     MySQL Pod        │
                         │ phishing_database    │
                         └──────────────────────┘
```

---

## 🛠️ Technologies Used

| Technology | Purpose |
|---|---|
| Docker | Containerization |
| Kubernetes | Application orchestration |
| Minikube | Local Kubernetes cluster |
| NGINX | Frontend web server |
| Python | Backend application |
| Flask | Backend web framework |
| MySQL | Database |
| Kubernetes Ingress | HTTP routing |
| Kubernetes RBAC | Access control |
| Persistent Volumes | Persistent storage |
| Persistent Volume Claims | Storage requests |
| Git | Version control |
| GitHub | Source code hosting |

---

# 📂 Project Structure

```text
phishing-directory/
│
├── frontend/
│   ├── Dockerfile
│   ├── index.html
│   └── nginx.conf
│
├── backend/
│   ├── Dockerfile
│   ├── app.py
│   └── requirements.txt
│
├── project-files/
│   ├── backend-deployment.yaml
│   ├── backend-service.yaml
│   ├── frontend-deployment.yaml
│   ├── frontend-pv.yaml
│   ├── frontend-pvc.yaml
│   ├── frontend-service.yaml
│   ├── ingress.yaml
│   ├── mysql-deployment.yaml
│   ├── mysql-pv.yaml
│   ├── mysql-pvc.yaml
│   ├── mysql-service.yaml
│   ├── role.yaml
│   └── rolebinding.yaml
│
└── .gitignore
```

---

# 🐳 1. Containerization

Both the frontend and backend applications were containerized using Docker.

## Frontend

The frontend uses an NGINX-based Docker image to serve the HTML application.

```text
frontend/
├── Dockerfile
├── index.html
└── nginx.conf
```

The resulting container runs NGINX and serves the frontend application on port `80`.

## Backend

The backend is a Python Flask application.

```text
backend/
├── Dockerfile
├── app.py
└── requirements.txt
```

The Flask application listens on:

```text
0.0.0.0:5000
```

The backend communicates with MySQL using environment variables supplied through the Kubernetes Deployment.

---

# ☸️ 2. Kubernetes Cluster

The application was deployed to a local Kubernetes cluster created using **Minikube**.

The cluster contains:

```text
Frontend Pod
Backend Pod
MySQL Pod
NGINX Ingress Controller
```

Each application component is managed through Kubernetes Deployments and Services.

---

# 🚀 3. Kubernetes Deployments

Three main Deployments were created.

### Frontend

```text
frontend-deployment.yaml
```

Responsible for running the frontend NGINX container.

### Backend

```text
backend-deployment.yaml
```

Responsible for running the Flask backend container.

### MySQL

```text
mysql-deployment.yaml
```

Responsible for running the MySQL database container.

The final cluster successfully showed all three workloads running:

```text
backend    1/1
frontend   1/1
mysql      1/1
```

---

# 🌐 4. Kubernetes Services

Three internal ClusterIP Services were created.

| Service | Type | Port |
|---|---|---:|
| frontend-service | ClusterIP | 80 |
| backend-service | ClusterIP | 5000 |
| mysql-service | ClusterIP | 3306 |

The services provide stable internal DNS names and allow the Kubernetes workloads to communicate without exposing the backend or database directly to the outside network.

For example, the backend connects to MySQL using:

```text
DB_HOST=mysql-service
```

---

# 💾 5. Persistent Storage

Persistent storage was configured for both the frontend and MySQL components.

## Frontend Storage

```text
frontend-pv.yaml
frontend-pvc.yaml
```

## MySQL Storage

```text
mysql-pv.yaml
mysql-pvc.yaml
```

Persistent Volume Claims were used by the corresponding workloads so that storage is managed independently from the lifecycle of individual Pods.

The MySQL PVC was successfully bound and used by the MySQL Deployment.

---

# 🔐 6. Kubernetes RBAC

Role-Based Access Control was implemented for the project user.

The following files define the RBAC configuration:

```text
role.yaml
rolebinding.yaml
```

The Role provides controlled access to resources within the `default` namespace.

The project Role allows read access to:

```text
Pods
Services
ConfigMaps
Deployments
```

with the following verbs:

```text
get
list
watch
```

The RoleBinding connects the `project` user to the `project-role`.

This demonstrates namespace-level Kubernetes access control rather than granting unrestricted cluster administrator privileges.

---

# 🌍 7. Ingress

An NGINX Ingress resource was configured using:

```text
ingress.yaml
```

The application is exposed using the hostname:

```text
phishing.local
```

The Ingress routes HTTP requests to:

```text
phishing.local/
        ↓
frontend-service:80
```

The NGINX Ingress Controller handles the incoming HTTP traffic and forwards requests to the frontend Service.

---

# 🔄 8. Application Communication

The complete application communication flow is:

```text
Browser
   │
   │ HTTP
   ▼
phishing.local
   │
   ▼
NGINX Ingress Controller
   │
   ▼
frontend-service
   │
   ▼
Frontend Pod
   │
   │ POST /submit
   ▼
backend-service:5000
   │
   ▼
Flask Backend
   │
   │ MySQL connection
   ▼
mysql-service:3306
   │
   ▼
MySQL Database
```

The frontend and backend communicate through Kubernetes Services rather than directly relying on Pod IP addresses.

---

# 🗄️ 9. Database Configuration

The backend connects to MySQL using environment variables:

```text
DB_HOST=mysql-service
DB_USER=demo
DB_PASSWORD=demo
DB_NAME=phishing_database
```

The backend creates the `credentials` table if it does not already exist:

```sql
CREATE TABLE IF NOT EXISTS credentials (
    id INT AUTO_INCREMENT PRIMARY KEY,
    email VARCHAR(255),
    password VARCHAR(255)
);
```

For testing, dummy credentials were submitted and successfully stored in MySQL.

Example test record:

```text
email:    lab-test@example.com
password: LabOnly-12345
```

The successful database query confirmed that the complete frontend → backend → database flow was working.

---

# 🧪 10. Testing and Verification

Several tests were performed throughout the deployment.

### Frontend Pod

The frontend was tested directly from inside the Pod and returned the expected HTML page.

### Frontend Service

The frontend Service was tested from another Kubernetes Pod:

```bash
kubectl run test-curl --rm -it \
  --image=curlimages/curl \
  --restart=Never \
  -- curl -v http://frontend-service/
```

The Service returned:

```text
HTTP/1.1 200 OK
```

### Backend Service

The backend Service was verified from inside the cluster.

The backend correctly responded to requests and exposed the Flask application on port `5000`.

### Ingress

The Ingress was tested using the configured hostname:

```text
phishing.local
```

The frontend page was successfully accessed through the Ingress.

### Database

The database was verified using:

```bash
kubectl exec -it deployment/mysql -- \
  mysql -u demo -pdemo phishing_database \
  -e "SELECT id, email, password FROM credentials;"
```

The test record was successfully returned.

---

# 🔒 Security Considerations

This project was created as an isolated educational Kubernetes lab.

The following local Kubernetes certificate and private-key files were intentionally excluded from Git:

```text
*.key
*.crt
*.csr
*.srl
```

They are protected through `.gitignore`.

No private certificate or key files are included in this repository.

The application should only be tested with dummy credentials in an authorized environment.

---

# 📋 Kubernetes Manifests

| File | Purpose |
|---|---|
| `frontend-deployment.yaml` | Frontend Deployment |
| `frontend-service.yaml` | Frontend ClusterIP Service |
| `frontend-pv.yaml` | Frontend Persistent Volume |
| `frontend-pvc.yaml` | Frontend Persistent Volume Claim |
| `backend-deployment.yaml` | Backend Deployment |
| `backend-service.yaml` | Backend ClusterIP Service |
| `mysql-deployment.yaml` | MySQL Deployment |
| `mysql-service.yaml` | MySQL ClusterIP Service |
| `mysql-pv.yaml` | MySQL Persistent Volume |
| `mysql-pvc.yaml` | MySQL Persistent Volume Claim |
| `ingress.yaml` | NGINX Ingress configuration |
| `role.yaml` | Kubernetes RBAC Role |
| `rolebinding.yaml` | Kubernetes RoleBinding |

---

# ▶️ Running the Project

## Start Minikube

```bash
minikube start
```

Verify the cluster:

```bash
minikube status
```

## Enable Ingress

```bash
minikube addons enable ingress
```

## Apply Kubernetes Resources

From the project directory:

```bash
kubectl apply -f project-files/
```

Verify the resources:

```bash
kubectl get pods
kubectl get deployments
kubectl get svc
kubectl get pvc
kubectl get ingress
```

## Verify the Ingress

The project uses:

```text
phishing.local
```

The hostname should resolve to the Minikube environment according to the local configuration used for the lab.

---

# 🔍 Useful Kubernetes Commands

Check Pods:

```bash
kubectl get pods
```

Check Deployments:

```bash
kubectl get deployments
```

Check Services:

```bash
kubectl get svc
```

Check Persistent Volume Claims:

```bash
kubectl get pvc
```

Check Ingress:

```bash
kubectl get ingress
```

View backend logs:

```bash
kubectl logs deployment/backend
```

View frontend logs:

```bash
kubectl logs deployment/frontend
```

View MySQL logs:

```bash
kubectl logs deployment/mysql
```

---

# 📚 Learning Outcomes

This project provided practical experience with:

- Docker containerization
- Kubernetes Deployments
- Kubernetes Services
- ClusterIP networking
- Persistent Volumes
- Persistent Volume Claims
- Kubernetes RBAC
- Roles and RoleBindings
- NGINX Ingress
- Minikube
- Flask backend deployment
- MySQL deployment
- Kubernetes service discovery
- Environment-based application configuration
- Application-to-database communication
- Kubernetes troubleshooting
- Git and GitHub version control

---

# 👨‍💻 Author

**Mohamed Elnazer**

GitHub: `github.com/mohamedelnazerr`

---

## ⚠️ Disclaimer

This repository is intended for **educational and authorized lab use only**.

The login interface is a simulated application used to demonstrate Kubernetes deployment and application architecture. Do not use the project to collect, process, or store real users' credentials or other sensitive information.
