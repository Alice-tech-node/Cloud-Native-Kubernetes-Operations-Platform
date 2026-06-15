## **Kubernetes Operations Platform**

## **🚀 Overview**

This project is a production-style Kubernetes operations platform that demonstrates real-world DevOps practices including:

*. Monitoring and observability*

*. Centralized logging*

*. GitOps-based deployments*

*. Kubernetes security controls*

*. Multi-tier application deployment*

## **Architecture**
<img width="2692" height="2129" alt="mermaid-diagram" src="https://github.com/user-attachments/assets/57c8d77d-c274-4562-a0ed-62ec1ebd4630" />



It is built using Kubernetes with pre-built container images.

*. 🧠 Architecture Overview* _ *. ☁️ Cloud Architecture*

*. 🏗️ Tech Stack*     _        *. ☸️ Kubernetes (Minikube / Cluster)*

*. 📦 Helm*           _        *. 📊 Prometheus*

*. 📈 Grafana*        _        *. 📜 Loki*

*. 🚀 Argo CD*        _         *. 🐘 PostgreSQL*

*. ⚡ Redis*          _         *. 🌐 NGINX Ingress*

*. 📦 Components*     _        *. 🖥️ Application Layer*


**NGINX frontend**

*. Backend API services*

*. PostgreSQL database (StatefulSet)*

*. Redis cache*


**Monitoring**

*. Prometheus collects metrics*

*. Grafana visualizes dashboards*


**Metrics include:**

*. CPU usage**

*. Memory usage*

*. Pod health*

*. Node performance*


**Logging**

*. Loki aggregates logs*

*. Centralized log querying via Grafana*

**GitOps**

*. Argo CD manages deployments*

*. GitHub is the single source of truth*

*. Automatic sync between Git and cluster*

**Security**

*RBAC (Role-Based Access Control)*

*Kubernetes Secrets for sensitive data*

*Network Policies for traffic control*


## **⚙️ Installation & Setup**
## **PHASE 1: CLUSTER STEP**
This phase establishes the Kubernetes environment by deploying and configuring the cluster, installing the required tools, and enabling core services such as Ingress to provide a foundation for hosting and managing applications.

**1. Install Tools**

Install:

*Minikube* , *kubectl* , *Helm*

Start Kubernetes cluster: *minikube start*

Verify cluster: *kubectl get nodes*

**2. Enable Ingress Controller**

Enable: *minikube addons enable ingress*

Confirm: *kubectl get pods -n ingress-nginx*

## **PHASE 2: MULTI-TIER APPLICATION DEPLOYMENT**

This phase deploys the core application components, including the frontend, database, and cache layers, demonstrating how multiple services communicate and operate together within a Kubernetes cluster.

**1. Deploy NGINX Frontend** 

*kubectl create deployment nginx --image=nginx*
*kubectl expose deployment nginx --port=80*

**2. Deploy PostgreSQL Database**

*helm repo add bitnami https://charts.bitnami.com/bitnami*
*helm install postgres bitnami/postgresql*

**3. Deploy Redis Cache**

*helm install redis bitnami/redis*

**4. Configure Ingress Routing**

apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: app-ingress
spec:
  rules:
  - host: app.local
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: nginx
            port:
              number: 80

Apply: kubectl apply -f ingress.yaml

## **PHASE 3: MONITORING LAYER**

This phase introduces observability by deploying Prometheus and Grafana to collect, store, and visualize cluster and application metrics, enabling proactive monitoring of system health and performance.

**1. Install Prometheus + Grafana**

*helm repo add prometheus-community https://prometheus-community.github.io/helm-charts*
*helm repo update*
*helm install monitoring prometheus-community/kube-prometheus-stack*

**2. Access Grafana**

*kubectl port-forward svc/monitoring-grafana 3000:80*

*Login: Username & Password*
*kubectl get secret monitoring-grafana \
-o jsonpath="{.data.username-password}" | base64 -d*

**3. Monitor Metrics**

You will monitor:
*CPU usage*
*Memory usage*
*Pod health*
*Node performance*
*NGINX traffic*

## **PHASE 4: LOGGING LAYER**
This phase implements centralized logging using Loki, allowing logs from applications and Kubernetes resources to be aggregated, searched, and analyzed from a single location for easier troubleshooting and auditing.

**1. Install Loki Stack**
*helm repo add grafana https://grafana.github.io/helm-charts*
*helm repo update*
*helm install loki grafana/loki-stack*

**2. Connect Loki to Grafana**
*Inside Grafana: Settings → Data Sources → Add Loki*

**3. View Logs**
*kubectl logs deployment/nginx*

Logs will appear in Grafana.

## **PHASE 5: GitOps (ARGO CD)**
This phase automates application deployment and configuration management using Argo CD, ensuring that the Kubernetes environment continuously synchronizes with the desired state defined in a Git repository

**1. Install Argo CD**
*kubectl create namespace argocd*
*kubectl apply -n argocd \
-f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml*

**2. Access Argo CD UI**
*kubectl port-forward svc/argocd-server -n argocd 8080:443*
*Open: https://localhost:8080*

**3. Get Admin Password**
*kubectl -n argocd get secret argocd-initial-admin-secret \
-o jsonpath="{.data.password}" | base64 -d*

**4. Connect Git Repository**
*Add your GitHub repo*
*Store Kubernetes manifests*
*Enable Auto Sync*

**5. GitOps Workflow**
*GitHub → Argo CD → Kubernetes Cluster*


  ***Any change pushed to GitHub is automatically deployed.***
  

## **PHASE 6: SECURITY LAYER**
This phase strengthens the platform's security posture by implementing Role-Based Access Control (RBAC), Secrets management, and Network Policies to protect sensitive information and control access between users and workloads.

**1. Create Namespace**
*kubectl create namespace secure-app*

**2. RBAC Setup**
*kind: Role
apiVersion: rbac.authorization.k8s.io/v1*

**3. Create Secrets**
*kubectl create secret generic app-secret \
--from-literal=password=SecurePass123*

**4. Network Policies**
*Restrict traffic between services.*

**Key Features**

✔ Multi-tier microservices architecture

✔ Full observability stack (metrics + logs)

✔ GitOps automated deployments

✔ Secure Kubernetes configuration

✔ Scalable workloads


**Learning Outcomes**

After completing this project, I understood:

. Kubernetes architecture

. Production monitoring systems

. Logging and observability

. GitOps workflows

. Cloud-native security

. Real-world DevOps operations


## **🧑‍💻 Author**

Name: Alice Oripu

Focus: Cloud & DevOps Engineering

GitHub: https://github.com/Alice-tech-node

**⭐ Project Status**

Status: In Progress 

Environment: Local (Minikube) / Cloud-ready
