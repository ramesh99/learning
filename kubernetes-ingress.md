# 🌐 Kubernetes Ingress

> [!NOTE]
> **Ingress** is a Kubernetes API resource that manages **external HTTP and HTTPS access** to services running inside a Kubernetes cluster.
>
> Instead of exposing every application using a separate `LoadBalancer` or `NodePort`, Ingress provides a **single entry point** and routes traffic to the appropriate service based on defined rules.

---

# 📚 Table of Contents

- What is Ingress?
- Why Use Ingress?
- Ingress Architecture
- How Ingress Works
- What is an Ingress Controller?
- Popular Ingress Controllers
- Example Ingress YAML
- Request Flow
- Benefits
- Summary
- Reference

---

# What is Ingress?

Ingress exposes **HTTP** and **HTTPS** routes from **outside the Kubernetes cluster** to services running **inside the cluster**.

Traffic routing is controlled by **rules** defined in the **Ingress Resource**.

An Ingress can be configured to:

- 🌍 Give services externally reachable URLs
- ⚖️ Load balance incoming traffic
- 🔒 Terminate SSL/TLS (HTTPS)
- 🌐 Support name-based virtual hosting
- 🛣️ Route traffic based on hostnames and paths

---

## 📦 Why Use Ingress?

Without Ingress:

```
Internet
    │
    ├── LoadBalancer → Service A
    ├── LoadBalancer → Service B
    ├── LoadBalancer → Service C
    └── LoadBalancer → Service D
```

Problems:

- Multiple LoadBalancers
- Higher cloud cost
- Difficult management
- Separate public IPs

---

With Ingress:

```
                Internet
                    │
                    ▼
          +--------------------+
          |   Ingress          |
          +--------------------+
              │     │      │
              ▼     ▼      ▼
         ServiceA ServiceB ServiceC
```

Advantages:

- Single entry point
- One public IP
- Centralized routing
- Easier SSL management

---

# 🏗️ Ingress Architecture

```text
                     Internet
                         │
                  HTTPS / HTTP
                         │
                 +----------------+
                 | Ingress        |
                 | Resource       |
                 +----------------+
                         │
                         ▼
              +----------------------+
              | Ingress Controller   |
              | (NGINX / Traefik)    |
              +----------------------+
                 │          │
                 ▼          ▼
          Service A     Service B
              │              │
              ▼              ▼
           Pod A          Pod B
```

---

# ⚙️ How Ingress Works

```text
Client Request

https://myapp.example.com/app1

        │
        ▼

Ingress Resource
(matches host & path)

        │
        ▼

Ingress Controller

        │
        ▼

app1-service

        │
        ▼

Pod
```

---

# 🎯 What is an Ingress Controller?

> [!IMPORTANT]
> **An Ingress resource alone does nothing.**
>
> Kubernetes only stores the routing rules.
>
> To make those rules work, an **Ingress Controller** must be installed in the cluster.

The controller continuously watches for Ingress resources and configures the underlying networking components accordingly.

Without an Ingress Controller:

```
Ingress Resource

     ❌ No Controller

Nothing happens
```

With an Ingress Controller:

```
Ingress Resource
        │
        ▼
Ingress Controller
        │
        ▼
Traffic routed to Services
```

---

# 🔧 Responsibilities of an Ingress Controller

The Ingress Controller:

- Watches Ingress resources
- Reads routing rules
- Configures reverse proxy
- Routes requests
- Load balances traffic
- Terminates SSL/TLS
- Handles hostname-based routing
- Handles path-based routing

---

# ⭐ Popular Ingress Controllers

| Controller | Description |
|------------|-------------|
| **NGINX Ingress** | Most widely used Kubernetes Ingress Controller |
| **Traefik** | Cloud-native reverse proxy with automatic service discovery |
| **HAProxy Ingress** | High-performance load balancer and proxy |
| **Istio Gateway** | Advanced traffic management using a service mesh |
| **Contour** | Envoy-based Kubernetes Ingress Controller |
| **AWS Load Balancer Controller** | Native AWS ALB integration |
| **Azure Application Gateway Ingress Controller (AGIC)** | Azure-native Application Gateway integration |
| **GKE Ingress** | Google Cloud native Ingress |

---

# 📝 Example Ingress Resource

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress

metadata:
  name: example-ingress

spec:
  rules:
    - host: myapp.example.com

      http:
        paths:

          - path: /app1
            pathType: Prefix

            backend:
              service:
                name: app1-service
                port:
                  number: 80

          - path: /app2
            pathType: Prefix

            backend:
              service:
                name: app2-service
                port:
                  number: 80
```

---

# 🔍 Understanding the YAML

| Field | Description |
|--------|-------------|
| `apiVersion` | Kubernetes API version |
| `kind` | Resource type (`Ingress`) |
| `metadata.name` | Name of the Ingress |
| `spec.rules` | Routing rules |
| `host` | Domain name |
| `http.paths` | URL paths |
| `path` | Incoming request path |
| `pathType` | Prefix or Exact matching |
| `backend.service.name` | Destination Kubernetes Service |
| `backend.service.port` | Service port |

---

# 🚦 Request Routing Example

Suppose the client sends:

```
https://myapp.example.com/app1
```

Ingress checks:

```
Host:
myapp.example.com

Path:
/app1
```

Routes to:

```
app1-service
      │
      ▼
Application Pods
```

---

Another request:

```
https://myapp.example.com/app2
```

Routes to:

```
app2-service
      │
      ▼
Application Pods
```

---

# 📊 Complete Request Flow

```text
                 User

                  │

                  ▼

      https://myapp.example.com/app1

                  │

                  ▼

           Ingress Resource

                  │

                  ▼

        Ingress Controller
     (NGINX / Traefik / HAProxy)

                  │

                  ▼

          app1-service (ClusterIP)

                  │

                  ▼

               Pod 1
               Pod 2
               Pod 3
```

---

# ✅ Benefits of Ingress

- 🌍 Single public entry point
- 💰 Reduces LoadBalancer costs
- 🔒 SSL/TLS termination
- ⚖️ Built-in load balancing
- 🛣️ Path-based routing
- 🌐 Host-based routing
- 🚀 Easier application exposure
- 🔧 Centralized traffic management
- 📈 Scalable architecture

---

# 💡 Summary

> [!TIP]
>
> - **Ingress** defines **how traffic should be routed**.
> - **Ingress Controller** is the component that **implements those routing rules**.
> - Without an **Ingress Controller**, an **Ingress resource has no effect**.
> - Common controllers include **NGINX**, **Traefik**, and **HAProxy**.

---

# 🔗 Reference

Official Kubernetes Documentation:

**Ingress Controllers**

https://kubernetes.io/docs/concepts/services-networking/ingress-controllers/

---
```

# 📊 Kubernetes Ingress Traffic Flow

> [!NOTE]
> The diagram below illustrates how **incoming HTTP/HTTPS traffic** enters a Kubernetes cluster through an **Ingress Controller**, which routes requests to the appropriate **Service**, and finally to the target **Pods**.

---

# 🏗️ High-Level Architecture

```text
                        Internet
                            │
                            ▼
                 Incoming HTTP / HTTPS Requests
                            │
                            ▼
                 Ingress Managed Load Balancer
                            │
                            ▼
┌─────────────────────────────────────────────────────────────────────────────┐
│                     Kubernetes Cluster                                      │
│                                                                             │
│  ┌───────────────────────────────────────────────────────────────────────┐  │
│  │                 Kubernetes Ingress Controller                         │  │
│  │                                                                       │  │
│  │  Rule 1: example.com/accounts ───────────────┐                        │  │
│  │                                              │                        │  │
│  │  Rule 2: example.com/loans ──────────────────┼─────────────┐          │  │
│  │                                              │             │          │  │
│  │  Rule 3: example.com/cards ──────────────────┘             │          │  │
│  └────────────────────────────────────────────────────────────┼──────────┘  │
│                                                               │             │
│                                                               ▼             │
│             ┌──────────────────────────────────────────────┐                │
│             │             Kubernetes Services              │                │
│             │                                              │                │
│             │  accounts-service                            │                │
│             │  loans-service                               │                │
│             │  cards-service                               │                │
│             └───────────────┬───────────────┬──────────────┘                │
│                             │               │                               │
│               ┌─────────────┘               └──────────────┐                │
│               ▼                                            ▼                │
│       Accounts Pods                                 Loans Pods              │
│       ┌───────┐ ┌───────┐                         ┌───────┐ ┌───────┐        │
│       │ Pod 1 │ │ Pod 2 │                         │ Pod 1 │ │ Pod 2 │        │
│       └───────┘ └───────┘                         └───────┘ └───────┘        │
│                                                                             │
│                               Cards Pods                                    │
│                           ┌───────┐ ┌───────┐                               │
│                           │ Pod 1 │ │ Pod 2 │                               │
│                           └───────┘ └───────┘                               │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

# 🔀 Path-Based Routing

The Ingress Controller inspects the incoming URL path and forwards traffic to the appropriate Kubernetes Service.

```text
Incoming Request
        │
        ▼
https://example.com/accounts
        │
        ▼
Ingress Rule:
/accounts
        │
        ▼
accounts-service
        │
        ▼
accounts Pods
```

---

```text
Incoming Request
        │
        ▼
https://example.com/loans
        │
        ▼
Ingress Rule:
/loans
        │
        ▼
loans-service
        │
        ▼
loans Pods
```

---

```text
Incoming Request
        │
        ▼
https://example.com/cards
        │
        ▼
Ingress Rule:
/cards
        │
        ▼
cards-service
        │
        ▼
cards Pods
```

---

# 🌐 Complete Traffic Flow

```text
                    Client Browser
                          │
                          ▼
              https://example.com/cards
                          │
                          ▼
                External Load Balancer
                          │
                          ▼
               Kubernetes Ingress Controller
                          │
        ┌─────────────────┼─────────────────┐
        │                 │                 │
        ▼                 ▼                 ▼
   /accounts         /loans            /cards
        │                 │                 │
        ▼                 ▼                 ▼
 accounts-service   loans-service   cards-service
        │                 │                 │
        ▼                 ▼                 ▼
   Accounts Pods     Loans Pods       Cards Pods
```

---

# 📋 Routing Rules Mapping

| Incoming URL | Ingress Rule | Kubernetes Service | Target Pods |
|--------------|--------------|-------------------|-------------|
| `/accounts` | `example.com/accounts` | `accounts-service` | Accounts Pods |
| `/loans` | `example.com/loans` | `loans-service` | Loans Pods |
| `/cards` | `example.com/cards` | `cards-service` | Cards Pods |

---

# 🚀 Request Processing Sequence

```text
1. User sends HTTP/HTTPS request
                │
                ▼
2. External Load Balancer receives traffic
                │
                ▼
3. Request forwarded to Ingress Controller
                │
                ▼
4. Ingress checks Host + Path rules
                │
                ▼
5. Matching Kubernetes Service selected
                │
                ▼
6. Service load balances request
                │
                ▼
7. One of the backend Pods processes the request
                │
                ▼
8. Response returned to the client
```

---

# 💡 Diagram Summary

> [!TIP]
>
> **Traffic Flow**
>
> ```text
> Internet
>    │
>    ▼
> Load Balancer
>    │
>    ▼
> Ingress Controller
>    │
>    ├── /accounts ──► accounts-service ──► Accounts Pods
>    │
>    ├── /loans ─────► loans-service ─────► Loans Pods
>    │
>    └── /cards ─────► cards-service ─────► Cards Pods
> ```
>
> The **Ingress Controller** acts as the central traffic router, directing requests to the correct Kubernetes Service based on the defined routing rules.

