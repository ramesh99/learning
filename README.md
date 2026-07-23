# 🚀 Kubernetes: Complete Technical Architecture Guide

---

## Table of Contents

1. [Kubernetes Cluster Overview](#kubernetes-cluster-overview)
2. [Cluster Architecture](#cluster-architecture)
3. [Master Node Components](#master-node-components)
4. [Worker Node Components](#worker-node-components)
5. [Kubernetes Services](#kubernetes-services)
6. [Setup & Configuration](#setup--configuration)
7. [ConfigMaps & Secrets](#configmaps--secrets)

---

## 🎯 Kubernetes Cluster Overview

### What is a Kubernetes Cluster?

A **Kubernetes Cluster** is a set of servers or virtual machines that work together to deliver desired outputs to end users. It ensures that microservices are always running properly without any issues.

### Why Kubernetes Instead of Docker Compose?

| Aspect                 | Docker Compose    | Kubernetes                             |
| ---------------------- | ----------------- | -------------------------------------- |
| **Deployment Scope**   | Single server     | Multiple distributed nodes             |
| **Scalability**        | Limited           | Highly scalable (hundreds of services) |
| **Auto-Scaling**       | ❌ Not supported  | ✅ Supported                           |
| **Auto-Rollouts**      | ❌ Not supported  | ✅ Supported                           |
| **High Availability**  | ❌ Limited        | ✅ Built-in                            |
| **Multi-Node Support** | ❌ Single machine | ✅ Distributed cluster                 |

**Key Advantage:** Kubernetes provides automatic deployments, rollouts, and scaling – making it ideal for production applications with hundreds of microservices.

---

## 🏗️ Cluster Architecture

```
                    ┌─────────────────────────────────────────┐
                    │     KUBERNETES CLUSTER                  │
                    ├─────────────────────────────────────────┤
                    │                                         │
                    │  ┌──────────────────────────────────┐  │
                    │  │      MASTER NODE (Control)       │  │
                    │  ├──────────────────────────────────┤  │
                    │  │ • Kube API Server                │  │
                    │  │ • Scheduler                      │  │
                    │  │ • Controller Manager             │  │
                    │  │ • etcd (Cluster DB)              │  │
                    │  └──────────────────────────────────┘  │
                    │              │                          │
                    │    ┌─────────┼─────────┐               │
                    │    ▼         ▼         ▼               │
                    │  ┌────────┐┌────────┐┌────────┐       │
                    │  │Worker 1││Worker 2││Worker 3│       │
                    │  │ Node   ││ Node   ││ Node   │       │
                    │  └────────┘└────────┘└────────┘       │
                    │                                         │
                    └─────────────────────────────────────────┘
```

---

## 🎮 Master Node Components

The **Master Node** controls and maintains the entire Kubernetes cluster.

### 1. **Kube API Server** 🌐

- **Purpose:** Exposes APIs for external and internal communication
- **Functions:**
  - Accepts instructions via kubectl CLI or Admin UI
  - Communicates with worker nodes
  - Processes deployment, scaling, and management requests
- **Access Methods:**
  - `kubectl` CLI
  - Admin UI (Dashboard)

### 2. **Scheduler** 📍

- **Purpose:** Determines which worker node should handle deployments
- **Workflow:**
  ```
  User Request → Kube API Server → Scheduler
                                        ↓
                    Identifies Best Worker Node
                                        ↓
                    Returns Instructions → Kube API Server
                                        ↓
                    Sends to Worker Node
  ```
- **Key Responsibility:** Allocate resources optimally based on requirements

### 3. **Controller Manager** 🔧

- **Purpose:** Ensures desired state matches actual state
- **Responsibilities:**
  - Continuously tracks containers and worker nodes
  - Monitors health status
  - Automatically replaces unhealthy containers/nodes
  - Maintains desired replica count
  - Performs self-healing

| Task             | Action                                         |
| ---------------- | ---------------------------------------------- |
| Container Fails  | Creates new container to replace it            |
| Node Goes Down   | Provisions new node                            |
| Replica Mismatch | Adds/removes containers to match desired state |

### 4. **etcd** 🧠

- **Purpose:** Acts as the brain of the Kubernetes cluster
- **Function:** Distributed key-value database storing all cluster information
- **Stored Data:**
  - Desired state definitions
  - Replica counts
  - Configuration data
  - Resource definitions
- **Usage:** Controller Manager and Scheduler query etcd to understand desired state

```
┌─────────────────────────────────┐
│      etcd (Key-Value Store)     │
├─────────────────────────────────┤
│ accounts-replicas: 3            │
│ deployment-status: running      │
│ configserver-version: v1.2.0    │
│ eureka-url: http://eureka:8070  │
└─────────────────────────────────┘
         ▲                    ▲
         │                    │
    Controller Manager    Scheduler
```

---

## ⚙️ Worker Node Components

Worker nodes handle incoming traffic and run microservices.

### 1. **Kubelet** 👤

- **Purpose:** Agent running on all worker nodes
- **Function:**
  - Receives instructions from master node (via Kube API Server)
  - Manages pod lifecycle
  - Executes deployment commands
- **Responsibilities:**
  - Deploy containers
  - Monitor pod health
  - Report status to master

```
Master Node (Kube API Server)
         ↓
    Kubelet (Worker Node)
         ↓
  Deploy Accounts Service
  with 3 replicas
```

### 2. **Container Runtime** 🐳

- **Purpose:** Environment to run containerized applications
- **Most Common:** Docker
- **Installation:** Pre-installed on all worker nodes
- **Role:** Executes and manages containers

### 3. **Pod** 📦

- **Smallest Deployment Unit in Kubernetes**
- **Key Characteristics:**
  - Smallest deployable object
  - Contains one or more containers
  - Usually contains ONE container per pod
  - Provides isolation between microservices

| Characteristic      | Details                                          |
| ------------------- | ------------------------------------------------ |
| **Size**            | Smallest unit in Kubernetes                      |
| **Container Count** | Usually 1 (occasionally >1 with sidecar pattern) |
| **Isolation**       | One microservice per pod                         |
| **Communication**   | Via network within cluster                       |
| **Lifecycle**       | Managed by Deployment/ReplicaSet                 |

#### Pod Deployment Example

```
           Worker Node
    ┌──────────────────────┐
    │   Pod 1              │
    │  ┌────────────────┐  │
    │  │ Accounts μ-svc │  │
    │  └────────────────┘  │
    └──────────────────────┘

    ┌──────────────────────┐
    │   Pod 2              │
    │  ┌────────────────┐  │
    │  │ Cards μ-svc    │  │
    │  └────────────────┘  │
    └──────────────────────┘

    ┌──────────────────────┐
    │   Pod 3              │
    │  ┌────────────────┐  │
    │  │ Loans μ-svc    │  │
    │  └────────────────┘  │
    └──────────────────────┘
```

#### Sidecar Pattern (Advanced)

- Occasionally, a pod contains 2 containers
- One main container + one helper container
- Typical use case: logging/monitoring sidecar

### 4. **Kube Proxy** 🔌

- **Purpose:** Network proxy for container communication
- **Functions:**
  - Exposes containers to the outside world
  - Enables communication between containers in cluster
  - Manages network routing
  - Implements load balancing

```
          External User/Service
                    ↓
            Kube Proxy (Network)
                    ↓
        ┌───────────┴───────────┐
        ▼                       ▼
      Pod 1                   Pod 2
   Container A            Container B
```

---

## 🔐 Kubernetes Services

Services expose applications inside or outside the cluster.

### 1. **ClusterIP Service** 🔒 (Default)

**Purpose:** Internal communication between microservices

| Feature             | Details                                    |
| ------------------- | ------------------------------------------ |
| **Type**            | Default Service type                       |
| **Access Scope**    | ✅ Cluster internal only                   |
| **External Access** | ❌ Not accessible from outside             |
| **IP Assignment**   | Internal cluster IP (10.x.x.x)             |
| **Load Balancing**  | Automatic                                  |
| **Use Case**        | Microservice-to-microservice communication |

#### Architecture

```
        Kubernetes Cluster
    ┌──────────────────────────┐
    │                          │
    │  Pod A → ClusterIP ←Pod B│
    │         Service          │
    │           ↓              │
    │          Pod C           │
    │                          │
    └──────────────────────────┘

    ✔ Internal communication
    ✘ No internet access
```

#### YAML Configuration

```yaml
apiVersion: v1
kind: Service
metadata:
  name: my-service
spec:
  type: ClusterIP
  selector:
    app: my-app
  ports:
    - port: 80
      targetPort: 8080
```

**Example Use Cases:**

- Frontend ↔ Backend communication
- Backend ↔ Database communication
- Internal APIs

---

### 2. **NodePort Service** 🚪

**Purpose:** External access via node IP and static port

| Feature             | Details                          |
| ------------------- | -------------------------------- |
| **Type**            | Exposes on every node            |
| **External Access** | ✅ Accessible from outside       |
| **Port Range**      | 30000 - 32767                    |
| **Access Pattern**  | `<NodeIP>:<NodePort>`            |
| **Routing**         | NodePort → ClusterIP → Pod       |
| **Use Case**        | Development, testing, small apps |

#### Architecture

```
    External User
    192.168.1.100:30080
            ↓
    ┌───────────────────┐
    │  Kubernetes Node  │
    │  NodePort: 30080  │
    └─────────┬─────────┘
              ↓
        ClusterIP Service
              ↓
        ┌─────┴─────┐
        ↓           ↓
      Pod 1       Pod 2
```

#### YAML Configuration

```yaml
apiVersion: v1
kind: Service
metadata:
  name: my-nodeport-service
spec:
  type: NodePort
  selector:
    app: my-app
  ports:
    - port: 80
      targetPort: 8080
      nodePort: 30080
```

**⚠️ Note:** NodePort is rarely used in production. Prefer LoadBalancer Service instead.

---

### 3. **LoadBalancer Service** ⚡ (Production-Ready)

**Purpose:** External access via cloud load balancer

| Feature                  | Details                                     |
| ------------------------ | ------------------------------------------- |
| **Type**                 | Cloud-integrated service                    |
| **External Access**      | ✅ Full internet access                     |
| **Load Balancer**        | ✅ Automatic cloud LB creation              |
| **Public IP**            | ✅ Cloud provider assigns                   |
| **Supported Clouds**     | AWS, Azure, GCP, Oracle Cloud, DigitalOcean |
| **Traffic Distribution** | Even distribution across healthy pods       |
| **Production Ready**     | ✅ Yes                                      |

#### Architecture

```
                Internet
                   ↓
        Public IP (Cloud LB)
                   ↓
    ┌──────────────────────────┐
    │   Cloud Load Balancer    │
    └─────────┬────────────────┘
              ↓
        ClusterIP Service
              ↓
      ┌───────┴───────┐
      ↓               ↓
    Pod 1           Pod 2
      ↓               ↓
    Pod 3           Pod 4
```

#### YAML Configuration

```yaml
apiVersion: v1
kind: Service
metadata:
  name: my-loadbalancer-service
spec:
  type: LoadBalancer
  selector:
    app: my-app
  ports:
    - protocol: TCP
      port: 8071
      targetPort: 8071
```

#### Service Type Comparison Table

| Feature           | ClusterIP     | NodePort    | LoadBalancer     |
| ----------------- | ------------- | ----------- | ---------------- |
| Internal Access   | ✅            | ✅          | ✅               |
| External Access   | ❌            | ✅          | ✅               |
| Cloud Integration | ❌            | ❌          | ✅               |
| Auto Public IP    | ❌            | ❌          | ✅               |
| Production Ready  | ❌            | ❌          | ✅               |
| Cost              | Free          | Free        | Charged by Cloud |
| Use Case          | Microservices | Development | Production Apps  |

---

## 🛠️ Setup & Configuration

### Local Kubernetes Cluster Setup (Docker Desktop)

#### Prerequisites

- Docker Desktop installed
- Administrator access

#### Installation Steps

1. **Enable Kubernetes in Docker Desktop**
   - Open Docker Desktop
   - Settings → Kubernetes → Enable Kubernetes

2. **Verify Installation**
   ```bash
   kubectl version --client
   kubectl config get-contexts
   kubectl config use-context docker-desktop
   kubectl get nodes
   ```

#### Useful Commands

| Command                           | Purpose                   |
| --------------------------------- | ------------------------- |
| `kubectl get nodes`               | List all nodes in cluster |
| `kubectl get deployments`         | List all deployments      |
| `kubectl get services`            | List all services         |
| `kubectl get replicaset`          | List all replica sets     |
| `kubectl apply -f file.yml`       | Deploy from YAML          |
| `kubectl delete -f file.yml`      | Delete deployment         |
| `kubectl logs <pod-name>`         | View pod logs             |
| `kubectl describe pod <pod-name>` | Detailed pod info         |

#### Official Documentation

- Docker Desktop Kubernetes: https://docs.docker.com/desktop/use-desktop/kubernetes/

---

### Kubernetes Dashboard (Web UI)

#### Installation

```bash
kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v2.7.0/aio/deploy/recommended.yaml
```

#### Access Dashboard

```bash
kubectl proxy
# Open: http://localhost:8001/api/v1/namespaces/kubernetes-dashboard/services/https:kubernetes-dashboard:/proxy/
```

#### Get Access Token

```bash
kubectl -n kubernetes-dashboard create token admin-user
```

#### Documentation

- Official Guide: https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/

**⚠️ Note:** Kubernetes Dashboard is deprecated. Consider using **Headlamp** for new installations.

---

## 📝 Deployment Configuration

### Example: ConfigServer Microservice Deployment

#### Step 1: Create YAML File

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: configserver-deployment
  labels:
    app: configserver
spec:
  replicas: 1
  selector:
    matchLabels:
      app: configserver
  template:
    metadata:
      labels:
        app: configserver
    spec:
      containers:
        - name: configserver
          image: ramreach9/configserver:s12
          ports:
            - containerPort: 8071
---
apiVersion: v1
kind: Service
metadata:
  name: configserver
spec:
  selector:
    app: configserver
  type: LoadBalancer
  ports:
    - protocol: TCP
      port: 8071
      targetPort: 8071
```

#### Step 2: Deploy to Kubernetes

```bash
kubectl apply -f configserver.yml
```

#### Step 3: Verify Deployment

```bash
kubectl get deployments
kubectl get services
kubectl get replicaset
```

#### Step 4: Access the Service

```
http://<external-ip>:8071
```

---

## 🗂️ ConfigMaps & Secrets

### ConfigMap: Store Non-Sensitive Configuration

**Purpose:** Store environment variables and configuration data accessible to containers

#### What ConfigMap Stores

- Application profiles (dev, prod, staging)
- Database URLs
- Service endpoints
- API URLs
- Feature flags

#### Methods to Consume ConfigMap

1. Environment Variables
2. Command Line Arguments
3. Configuration Files in Volume

#### Create ConfigMap File

**File:** `configmaps.yml`

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rameshbank-configmap
data:
  # Application Configuration
  SPRING_PROFILES_ACTIVE: "prod"
  SPRING_CONFIG_IMPORT: "configserver:http://configserver:8071/"

  # Service URLs
  EUREKA_CLIENT_SERVICEURL_DEFAULTZONE: "http://eurekaserver:8070/eureka/"

  # Service Names
  CONFIGSERVER_APPLICATION_NAME: "configserver"
  EUREKA_APPLICATION_NAME: "eurekaserver"
  ACCOUNTS_APPLICATION_NAME: "accounts"
  LOANS_APPLICATION_NAME: "loans"
  CARDS_APPLICATION_NAME: "cards"
  GATEWAY_APPLICATION_NAME: "gatewayserver"

  # Keycloak Configuration
  KC_BOOTSTRAP_ADMIN_USERNAME: "admin"
  KC_BOOTSTRAP_ADMIN_PASSWORD: "admin"

  # Security Configuration
  SPRING_SECURITY_OAUTH2_RESOURCESERVER_JWT_JWK-SET-URI: "http://keycloak:7080/realms/master/protocol/openid-connect/certs"
```

#### Deploy ConfigMap

```bash
kubectl apply -f configmaps.yml
kubectl get configmap
kubectl describe configmap rameshbank-configmap
```

#### Use ConfigMap in Deployment

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: accounts-deployment
spec:
  replicas: 2
  template:
    spec:
      containers:
        - name: accounts
          image: ramreach9/accounts:latest
          envFrom:
            - configMapRef:
                name: rameshbank-configmap
```

#### ConfigMap Data Structure

| Key                                                     | Value                            | Purpose                |
| ------------------------------------------------------- | -------------------------------- | ---------------------- |
| `SPRING_PROFILES_ACTIVE`                                | prod                             | Active Spring profile  |
| `SPRING_CONFIG_IMPORT`                                  | configserver:http://...          | Config server location |
| `EUREKA_CLIENT_SERVICEURL_DEFAULTZONE`                  | http://eurekaserver:8070/eureka/ | Service discovery URL  |
| `KC_BOOTSTRAP_ADMIN_USERNAME`                           | admin                            | Keycloak admin user    |
| `SPRING_SECURITY_OAUTH2_RESOURCESERVER_JWT_JWK-SET-URI` | http://keycloak:7080/...         | JWT key endpoint       |

---

### Secret: Store Sensitive Configuration

**Purpose:** Store sensitive data like passwords, API keys, credentials (encrypted)

#### What Secret Stores

- Database passwords
- API keys
- OAuth tokens
- Private certificates
- Database credentials

#### Create Secret

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: rameshbank-secret
type: Opaque
data:
  DB_PASSWORD: cGFzc3dvcmQxMjM= # base64 encoded
  API_KEY: YWJjZGVmZ2hpams= # base64 encoded
```

#### Use Secret in Deployment

```yaml
envFrom:
  - secretRef:
      name: rameshbank-secret
```

---

## 📊 Complete Microservices Deployment Architecture

```
                          ┌─────────────────────────────────────────┐
                          │    KUBERNETES CLUSTER                   │
                          ├─────────────────────────────────────────┤
                          │                                         │
                          │  ConfigMap & Secrets (Environment Vars) │
                          │                                         │
    ┌─────────────────────┼─────────────────────┬──────────────────┐
    │                     │                     │                  │
    ▼                     ▼                     ▼                  ▼
┌─────────────┐  ┌──────────────┐  ┌─────────────┐  ┌──────────────┐
│ConfigServer │  │EurekaServer  │  │Gateway      │  │LoadBalancer  │
│ Pod (3)     │  │ Pod (2)      │  │ Pod (2)     │  │              │
└──────┬──────┘  └──────┬───────┘  └──────┬──────┘  └──────┬───────┘
       │                │                 │              External
       │                │                 │              Internet
       └────────┬───────┴────────┬────────┘
                │                │
    ┌───────────┴───────┐    ┌───┴────────────┐
    │                   │    │                │
    ▼                   ▼    ▼                ▼
┌────────────┐  ┌────────────┐  ┌────────────┐
│Accounts μ-s│  │Cards μ-s   │  │Loans μ-s   │
│ Pods (3)   │  │ Pods (3)   │  │ Pods (3)   │
└────────────┘  └────────────┘  └────────────┘
```

---

## 📋 Quick Reference Checklist

### Before Production Deployment

- [ ] Master node configured with Kube API Server, Scheduler, Controller Manager
- [ ] etcd database backed up and secured
- [ ] Worker nodes running Kubelet and Container Runtime
- [ ] LoadBalancer Service configured for external access
- [ ] ConfigMap created with all environment variables
- [ ] Secrets created for sensitive data (encrypted)
- [ ] All microservice deployments tested
- [ ] Health checks and monitoring enabled
- [ ] Logging and monitoring setup complete
- [ ] Disaster recovery plan in place

### Deployment Workflow

1. **Create ConfigMap** → Define environment variables
2. **Create Secrets** → Store sensitive data
3. **Create Deployments** → Deploy microservices with replicas
4. **Create Services** → Expose services (ClusterIP/LoadBalancer)
5. **Apply YAML Files** → `kubectl apply -f *.yml`
6. **Verify Deployment** → Check pods, services, and logs
7. **Test Endpoints** → Verify microservice communication
8. **Monitor & Scale** → Adjust replicas as needed

---

# Kubernetes Manifest File to Create ConfigMap

A Kubernetes ConfigMap is an essential Kubernetes resource used to store configuration data separately from the application code.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rameshbank-configmap
data:
  SPRING_PROFILES_ACTIVE: prod
```

The **apiVersion** and **kind** fields are required for all Kubernetes objects. When creating a config map inside K8s, the kind should be "ConfigMap".

The **metadata** field contains the name of the ConfigMap and other metadata about the object.

The **data** field is where the key-value pairs are stored. The keys can be any alphanumeric string, and the values can be strings, numbers, or binary data.

# Kubernetes Manifest File to Deploy a Container

In Kubernetes, a Deployment is a high-level resource used to manage the deployment and scaling of containerized applications. It provides a declarative way to define and maintain the desired state of your application. When you create a Deployment, Kubernetes ensures that the specified number of replicas of your application are running and automatically handles scaling, rolling updates, and rollbacks.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: accounts-deployment
  labels:
    app: accounts
spec:
  replicas: 1
  selector:
    matchLabels:
      app: accounts
  template:
    metadata:
      labels:
        app: accounts
    spec:
      containers:
        - name: accounts
          image: ramesh/accounts:latest
          ports:
            - containerPort: 8080
          env:
            - name: SPRING_PROFILES_ACTIVE
              valueFrom:
                configMapKeyRef:
                  name: rameshbank-configmap
                  key: SPRING_PROFILES_ACTIVE
```

The **apiVersion** and **kind** fields are required for all Kubernetes objects. For deployment manifest file, the kind should be "Deployment".

**Metadata:** The metadata section contains information about the Deployment, such as its name and labels. The Deployment is named "accounts-deployment", and it has the label "app: accounts".

**Spec:** The spec section defines the desired state of the Deployment.

**Replicas:** The replicas field is set to 1, indicating that only one replica of the container should be running at any given time.

**Selector:** The selector field is used to select the pods controlled by this Deployment. In this case, it's using the label "app: accounts" to identify the pods.

# Kubernetes Manifest File to Deploy a Container

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: accounts-deployment
  labels:
    app: accounts
spec:
  replicas: 1
  selector:
    matchLabels:
      app: accounts
  template:
    metadata:
      labels:
        app: accounts
    spec:
      containers:
        - name: accounts
          image: ramesh/accounts:latest
          ports:
            - containerPort: 8080
          env:
            - name: SPRING_PROFILES_ACTIVE
              valueFrom:
                configMapKeyRef:
                  name: rameshbank-configmap
                  key: SPRING_PROFILES_ACTIVE
```

**Template:** The template section specifies the pod template that will be used to create pods for this Deployment.

**Metadata:** The metadata section inside the template defines the labels for the pods. The pod will have the label "app: accounts".

**Spec:** The spec section inside the template specifies the details of the pod's specification.

**Containers:** The containers field lists the containers that should be part of the pod. In this case, there is one container named "accounts" based on the "ramesh/accounts:latest" image.

**Ports:** The ports section exposes port 8080 of the container.

**Environment Variables:** The env section sets an environment variable named "SPRING_PROFILES_ACTIVE" and assigns it a value from a ConfigMap.

**valueFrom:** The valueFrom field allows you to reference data from a ConfigMap. In this case, it is using configMapKeyRef to fetch the value of the "SPRING_PROFILES_ACTIVE" key from the ConfigMap named "ramesh-configmap".

# Kubernetes Manifest File to Create a Service

In Kubernetes, a Service is an essential resource that provides network connectivity to a set of pods. It acts as a stable endpoint for accessing and load balancing traffic across multiple replicas of a pod. Services abstract away the underlying network details, allowing pods to be more dynamic and scalable without affecting how clients access them.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: accounts-service
spec:
  selector:
    app: accounts
  type: ClusterIP
  ports:
    - protocol: TCP
      port: 8080
      targetPort: 8080
```

The **apiVersion** and **kind** fields are required for all Kubernetes objects. For service manifest file, the kind should be "Service".

**Metadata:** The metadata section contains information about the Service, such as its name.

**Spec:** The spec section defines the desired state of the Service.

**Selector:** The selector field is used to select the pods that the Service will route traffic to. In this case, it uses the label "app: accounts" to select the pods controlled by the Deployment with the same label.

**Type:** The type field specifies the type of Service. In this case, it is set to "ClusterIP," which means that the Service will be accessible only from within the cluster.

# Kubernetes Manifest File to Create a Service

```yaml
apiVersion: v1
kind: Service
metadata:
  name: accounts-service
spec:
  selector:
    app: accounts
  type: ClusterIP
  ports:
    - protocol: TCP
      port: 8080
      targetPort: 8080
```

**Ports:** The ports section defines the ports that the Service should listen on and forward traffic to.

**protocol:** The protocol field specifies the protocol used for the service port. In this case, it's TCP.

**port:** The port field is the port number on which the Service will listen for incoming traffic.

**targetPort:** The targetPort field is the port number on the pods to which the incoming traffic will be forwarded.


# Kubernetes Commands

A reference guide for deploying, monitoring, self-healing, and managing rollouts/rollbacks of microservices on a Kubernetes cluster.

---

## 1. Deploying Microservices

List existing services in the cluster:

```bash
kubectl get services
```

Deploy each microservice manifest in order:

```bash
kubectl apply -f 1_keycloak.yml          # Deploy Keycloak (auth service)
kubectl apply -f 2_configmaps.yml        # Deploy ConfigMaps
kubectl apply -f 3_configserver.yml      # Deploy Config Server
kubectl apply -f 4_eurekaserver.yml      # Deploy Eureka Server (service discovery)
kubectl apply -f 5_accounts.yml          # Deploy Accounts microservice
kubectl apply -f 6_loans.yml             # Deploy Loans microservice
kubectl apply -f 7_cards.yml             # Deploy Cards microservice
```

Check ReplicaSets to confirm the replica counts defined in the manifest files (e.g., `configserver.yml`):

```bash
kubectl get replicaset
```

Check running pods:

```bash
kubectl get pods
```

---

## 2. Automatic Self-Healing in the Cluster

Kubernetes automatically replaces pods that are deleted or fail, based on the desired state defined in the ReplicaSet.

```bash
# View current pods
kubectl get pods

# Delete a specific pod (simulate failure)
kubectl delete pod accounts-deployment-r438590484

# Kubernetes automatically creates a new pod to maintain the desired replica count
kubectl get replicaset
kubectl get pods
```

To view all cluster events sorted by creation timestamp:

```bash
kubectl get events --sort-by=.metadata.creationTimestamp
```

### Verify Services via Eureka Server

Check whether all microservices have registered and started correctly:

```
http://localhost:8070
```

Deploy the API Gateway:

```bash
kubectl apply -f 8_gateway.yml
```

> **Note:** In real production environments, DevOps/operations teams use more advanced strategies to deploy microservices in a predefined, controlled order.

---

## 3. Rollout & Rollback (Zero-Downtime Deployments)

### Scaling Deployments

Scale a deployment up or down:

```bash
kubectl scale deployment account-deployment --replicas=1
```

Check the current state:

```bash
kubectl get pods
kubectl get replicasets
```

Describe a specific pod for detailed info:

```bash
kubectl describe pod gatewayserver-deployment-45uirififhu
```

### Updating to a New Image Version

When rolling out a new version of an image, Kubernetes pulls the new image and terminates the old pod automatically:

```bash
kubectl set image deployment gatewayserver-deployment gatewayserver=ramreach9/gatewayserver:111 --record
```

Check the events to confirm rollout progress:

```bash
kubectl get events --sort-by=.metadata.creationTimestamp
```

### Rolling Back to a Previous Version

Rollback example: reverting an image update from version `s12` back to `s11`.

```bash
# View rollout/revision history
kubectl rollout history deployment gatewayserver-deployment

# Roll back to a specific revision (e.g., revision 1, which used image s11)
kubectl rollout undo deployment gatewayserver-deployment --to-revision=1

# Confirm the rollback
kubectl get pods
kubectl describe pod gatewayserver-deployment-453ijfidfjd
```

---

## 4. Deleting Deployments & Services

Remove deployed resources by deleting them in reverse order:

```bash
kubectl delete -f 8_gateway.yml
kubectl delete -f 7_cards.yml
```
