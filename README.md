# **Kubernetes Operations Platform**

# Table of Contents

- [Project Overview](#project-overview)
- [Cloud Architecture Diagram](#cloud-architecture-diagram)
- [Architecture Overview](#architecture-overview)
- [Installation & Setup](#installation--setup)
  - [Phase 1: Cluster Setup](#phase-1--cluster-setup)
  - [Phase 2: Multi-Tier Application Deployment](#phase-2--multi-tier-application-deployment)
  - [Phase 3: Monitoring Layer](#phase-3--monitoring-layer)
  - [Phase 4: Logging Layer](#phase-4--logging-layer)
  - [Phase 5: GitOps Layer](#phase-5--gitops-layer)
  - [Phase 6: Security Layer](#phase-6--security-layer)
  - [Phase 7: Final Validation & Integration Testing](#phase-7--final-validation--integration-testing)
- [Challenges and Lessons Learned](#challenges-and-lessons-learned)
- [Future Improvements](#future-improvements)
- [Learning Outcomes](#learning-outcomes)
- [Author](#author)


### **Project Overview**

The Kubernetes Operations Platform is a comprehensive, production-style project designed to demonstrate the core technologies and practices used in modern cloud-native environments. The project combines application deployment, monitoring, centralized logging, security controls, and GitOps automation into a single Kubernetes-based platform.
The platform is built around a multi-tier architecture consisting of a frontend application, a PostgreSQL database, and a Redis caching layer, all running within a Kubernetes cluster. To ensure visibility into the health and performance of the environment, Prometheus and Grafana are deployed to collect and visualize metrics from cluster resources and workloads. Centralized logging is implemented using Loki, enabling efficient log aggregation, analysis, and troubleshooting across the platform.


### **Cloud Architecture Diagram**
<img width="2692" height="2129" alt="mermaid-diagram" src="https://github.com/user-attachments/assets/57c8d77d-c274-4562-a0ed-62ec1ebd4630" />


### **Architecture Overview**

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


# **Installation & Setup**

## **Phase 1: Cluster Setup**
This phase establishes the Kubernetes environment by deploying and configuring the cluster, installing the required tools, and enabling core services such as Ingress to provide a foundation for hosting and managing applications.

**1. Install Tools**

Install:

*Minikube* , *kubectl* , *Helm*

Start Kubernetes cluster: *minikube start*

Verify cluster: *kubectl get nodes*
<img width="1237" height="457" alt="Cluster setup-minikube" src="https://github.com/user-attachments/assets/264eb69a-e0a8-4067-b458-ec32ea03c966" />

**2. Enable Ingress Controller**

Enable: *minikube addons enable ingress*

Confirm: *kubectl get pods -n ingress-nginx*
<img width="1083" height="272" alt="Enable ingress" src="https://github.com/user-attachments/assets/08a9be41-d223-4b51-b14c-559ccb9c585f" />

## **Phase 2: Multi-Tier Application Deployment**

This phase deploys the core application components, including the frontend, database, and cache layers, demonstrating how multiple services communicate and operate together within a Kubernetes cluster.

**1. Deploy NGINX Frontend** 

*kubectl create deployment nginx --image=nginx*
*kubectl expose deployment nginx --port=80*
<img width="825" height="147" alt="Nginx Frontend-Deploy" src="https://github.com/user-attachments/assets/20f3fa11-1945-4855-b6ca-a58b02ef2bad" />


**2. Deploy PostgreSQL Database**

*helm install postgres oci://registry-1.docker.io/bitnamicharts/postgresql*
<img width="938" height="293" alt="PostgresSQL -Deploy" src="https://github.com/user-attachments/assets/b446addc-f68f-44e9-b06e-1c732fa97a63" />

**3. Deploy Redis Cache**

*helm install redis oci://registry-1.docker.io/bitnamicharts/redis*
<img width="1076" height="295" alt="Redis-Deploy" src="https://github.com/user-attachments/assets/a84497fb-393e-48a2-8ac1-d29215f7fd4a" />

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

<img width="1082" height="407" alt="Ingress Routing" src="https://github.com/user-attachments/assets/fc619791-98f0-4bc3-b714-bdcc39394aee" />


*I began by deploying an NGINX frontend and exposing its service on port 80, followed by setting up the storage layers, seamlessly installing the Bitnami, PostgreSQL database, and Redis cache via Helm. Finally, I generated a configuration file to deploy our Ingress routing, successfully mapping the (app.local) host to our frontend. Your entire multi-tier architecture is now running, connected, and completely deployed within your Kubernetes cluster!*

## **Phase 3: Monitoring Layer**

This phase introduces observability by deploying Prometheus and Grafana to collect, store, and visualize cluster and application metrics, enabling proactive monitoring of system health and performance.

**1. Install Prometheus + Grafana**

*helm repo add prometheus-community https://prometheus-community.github.io/helm-charts*
*helm repo update*
*helm install monitoring prometheus-community/kube-prometheus-stack*
<img width="1082" height="357" alt="Prometheus and Grafana stack --Install" src="https://github.com/user-attachments/assets/a3875afe-9681-4bc6-ad7c-8eec07084b05" />

**2. Access Grafana**

*kubectl port-forward svc/monitoring-grafana 3000:80*

*Login: Username & Password*
*kubectl get secret monitoring-grafana \
-o jsonpath="{.data.username-password}" | base64 -d*
<img width="1267" height="113" alt="Grafana" src="https://github.com/user-attachments/assets/a1a3b53b-c457-47fd-b484-a2fc0912f2ad" />

**3. Monitor Metrics**

You will monitor:
*CPU usage*
*Memory usage*
*Pod health*
*Node performance*
*NGINX traffic*
<img width="1500" height="902" alt="Grafana monitor metric" src="https://github.com/user-attachments/assets/56771729-220f-4514-9163-289cd5ada22c" />


*I successfully completed Phase 3 by introducing a robust observability and monitoring layer to the Kubernetes cluster. By leveraging modern Helm OCI (Open Container Iniative) registries, I deployed the full Prometheus and Grafana metrics collection suite. I then used secure script commands to extract and decode the auto-generated administrative credentials directly from the cluster’s secrets. Finally, I established a live connection using a port-forward bridge to access the web browser interface at port 3000, logging directly into the Grafana Dashboard directory to monitor the cluster's overall health, pods, and compute resources.*

## **Phase 4: Logging Layer**
This phase implements centralized logging using Loki, allowing logs from applications and Kubernetes resources to be aggregated, searched, and analyzed from a single location for easier troubleshooting and auditing.

**1. Install Loki Stack**
*helm repo add grafana https://grafana.github.io/helm-charts*
*helm repo update*
*helm install loki grafana/loki-stack*
<img width="1066" height="306" alt="Loki stack -install" src="https://github.com/user-attachments/assets/a40218b3-191e-4865-88bd-7b096faa1b97" />

**2. Connect Loki to Grafana**
*Inside Grafana: Settings → Data Sources → Add Loki*
<img width="1796" height="792" alt="Connect Loki" src="https://github.com/user-attachments/assets/21e78832-193f-4355-a386-c392e067e01f" />

**3. View Logs**
*kubectl logs deployment/nginx*

Logs will appear in Grafana.
<img width="1503" height="900" alt="View logs" src="https://github.com/user-attachments/assets/710d9123-25ac-4f41-808b-329acb0fe141" />

*I deployed the Prometheus, Grafana, and Loki monitoring and logging stacks to gather platform telemetry, and subsequently optimized the cluster's resource footprints to drop host machine CPU usage.*

## **Phase 5: GitOps Layer**
This phase automates application deployment and configuration management using Argo CD, ensuring that the Kubernetes environment continuously synchronizes with the desired state defined in a Git repository

**1. Install Argo CD**
*kubectl create namespace argocd*
*kubectl apply -n argocd \
-f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml*
<img width="1060" height="550" alt="install Argo CD" src="https://github.com/user-attachments/assets/04fd5f2f-5358-4204-a383-a489c7a37217" />
**2. Access Argo CD UI**
*kubectl port-forward svc/argocd-server -n argocd 8080:443*
*Open: https://localhost:8080*
<img width="1317" height="898" alt="Access Argo" src="https://github.com/user-attachments/assets/d062e9b4-0ff3-4713-bf41-d9d15c05976b" />

**3. Get Admin Password**
*kubectl -n argocd get secret argocd-initial-admin-secret \
-o jsonpath="{.data.password}" | base64 -d*
<img width="1196" height="225" alt="admin secret" src="https://github.com/user-attachments/assets/f68253f1-d636-4457-9a5c-57268c76bfdb" />

**4. Connect Git Repository**
*Add your GitHub repo*
*Store Kubernetes manifests*
*Enable Auto Sync*
<img width="1311" height="207" alt="git repo" src="https://github.com/user-attachments/assets/e18de28f-751a-48da-a732-8eecada758f9" />
<img width="1118" height="796" alt="gitops workflow" src="https://github.com/user-attachments/assets/3883c5be-dfda-42d6-bbbd-17c42b9a19ac" />

**5. GitOps Workflow**
*GitHub → Argo CD → Kubernetes Cluster*
<img width="1118" height="796" alt="gitops workflow" src="https://github.com/user-attachments/assets/18f6ffe3-8f31-433d-a60d-c43e3d4ca201" />

  ### ***Any change pushed to GitHub is automatically deployed.***
*I configured an automated GitOps delivery pipeline by deploying Argo CD, opening a local port-forward network tunnel, and successfully syncing the web application manifests directly from GitHub repository.*

## **Phase 6: Security Layer**
This phase strengthens the platform's security posture by implementing Role-Based Access Control (RBAC), Secrets management, and Network Policies to protect sensitive information and control access between users and workloads.

**1. Create Namespace**
*kubectl create namespace secure-app*
<img width="970" height="182" alt="namespace" src="https://github.com/user-attachments/assets/74089974-e57d-406c-a75f-9d326ddd61e9" />

**2. RBAC Setup**
*kind: Role
apiVersion: rbac.authorization.k8s.io/v1*
<img width="1072" height="237" alt="RBAC" src="https://github.com/user-attachments/assets/c1686771-0d95-41be-bb65-e54e2a7e4208" />

**3. Create Secrets**
*kubectl create secret generic app-secret \
--from-literal=password=SecurePass123*
<img width="1082" height="177" alt="secret" src="https://github.com/user-attachments/assets/18c89ff2-4ae3-436f-98cc-bcbac08ab420" />

**4. Network Policies**
*Restrict traffic between services.*
<img width="948" height="320" alt="network traffic" src="https://github.com/user-attachments/assets/a3f2bcb7-04ea-47f9-8867-d4b83ad5aeed" />
<img width="857" height="221" alt="verify" src="https://github.com/user-attachments/assets/19d85364-6bf0-42d0-9a99-e3d42b7b20ea" />

*I established a robust zero-trust security architecture by building an isolated namespace, creating a restricted developer RBAC profile, injecting secure passwords, and applying a default-deny ingress network firewall.*

## **Phase 7: Final Validation & Integration Testing**
This phase verifies that all platform components are functioning together as intended by testing application availability, monitoring, logging, GitOps synchronization, and security controls to ensure a stable and production-ready environment.

Run:
*kubectl get pods*
*kubectl get svc*
*kubectl get ingress*

Confirm:
 - Apps running
 - Monitoring active
 - Logging working
 - GitOps synced
 - Security enforced
<img width="1655" height="937" alt="Validation and testing" src="https://github.com/user-attachments/assets/69a989fa-e77b-4aff-a696-39e183bf7ebb" />

*I executed comprehensive system-wide diagnostic commands to audit all running workloads, verify live monitoring/logging tools, confirm automated GitOps synchronization, and validate that all security layers were actively enforced.*

### **Challenges Faced**

- Conflict from Automated Background Scanners: The automated "sidecar" containers in the Helm chart were causing configuration clashes and redundant setups. I solved this by performing a full system purge and then reinstalling the stack while explicitly disabling these automated scanners.

- Service Connectivity Issues: Grafana struggled to resolve internal service addresses for Loki and Prometheus. I solved this by bypassing the default DNS and using explicit, fully-qualified service URLs to establish a reliable network path.

- Authentication Rejections (Multi-tenancy): Loki blocked connection attempts because it required a tenant identity header. I solved this by manually adding the X-Scope-OrgID custom HTTP header into the Grafana data source configuration.

- Persistent Configuration Loops: Old settings and cache files hindered our ability to reach a stable state. I solved this by performing a "nuclear" reset, which included deleting the Minikube virtual machine, clearing all persistent volumes, and wiping the local Kubernetes configuration to ensure a completely clean slate.
  
- High CPU & Cluster Deadlock: I stopped the resource-heavy monitoring and logging stacks by scaling their replicas to zero, which instantly dropped your computer's CPU from a staggering 84% and allowed core system components to recover.

- Stuck "Terminating" and "Pending" Pods: To clear the zombie processes and locked network ports that kept rolling updates frozen, I performed a complete hard restart of your PC to flush the RAM and reset the environment.

- Post-Reboot Connection Refusal: When minikube start was refused because the background engine wasn't awake yet, I manually launched Docker Desktop from Windows Start Menu and waited for the whale icon to turn solid green before running commands.

- Broken Portal Access to Argo CD: To fix the network drops and browser errors that occurred whenever an Argo CD server pod restarted, I re-established a dedicated connection bridge using a clean kubectl port-forward command in an open terminal window.
- 
**Lessons Learned**
- When troubleshooting fails, a fresh start is often the fastest path to success.
- Relying on default internal DNS resolution can be unreliable. Specifying exact, fully-qualified service URLs creates a much more stable and predictable connection between services.
- Loki have built-in security requirements (such as multi-tenancy) that will reject connections if the expected headers are missing, even if the network path is otherwise open.
- Containers are convenient for configuration, they can cause significant issues when they override manual, deliberate settings. Learning to disable these features (using Helm flags) allows us to take full control of the environment.
- When file systems and network ports get completely deadlocked under heavy stress, a full host machine reboot is the most efficient way to clear frozen processes.
- 

#### **Learning Outcomes**

After completing this project, I understood:

. Kubernetes architecture

. Mastering the "Nuclear" Reset

. Controlling Automated Infrastructure

. Cluster Verification & Auditing

. GitOps workflows

. Cloud-native security

. Real-world DevOps operations

. Understanding Security Requirements

. Resilient Infrastructure Recovery


### **Author**

Name: Alice Oripu

Focus: Cloud & DevOps Engineering

GitHub: https://github.com/Alice-tech-node

**Project Status**

Status: Completed

Environment: Local (Minikube) / Cloud-ready
