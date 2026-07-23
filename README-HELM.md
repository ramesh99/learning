# Introduction to Helm

## What is HELM?

Helm is renowned as the "package manager of Kubernetes," aiming to enhance the management of Kubernetes projects by offering users a more efficient approach to handling the multitude of YAML files involved.

### The Problem Without Helm

1. Without Helm we need to maintain all K8s manifest files for Deployment, Service, ConfigMap etc. for each microservice.

2. Without Helm, the DevOps team members have to manually apply or delete all the Kubernetes YAML manifest files using `kubectl`.

3. The path Helm takes to solve these issues is by using a packaging format called **charts**. A chart is a collection of files that describe a related set of Kubernetes resources.

### Helm Charts

A single Helm chart might be used to deploy a simple app or something complex like a full web app stack with HTTP servers, databases, caches, and so on.

A chart can have child charts and dependent charts as well. This means that Helm can install a whole dependency tree of a project with just a single command.

# Helm Chart Structure

```
wordpress/
├── Chart.yml
├── values.yml
├── charts/
└── templates/
```

- **Top level `wordpress` folder** is the name of the chart that we have given while installing/creating the chart.

- **`Chart.yaml`** will have meta info about the helm chart
- **`values.yaml`** will have dynamic values for the chart

- **`charts` folder** will have other charts which the current chart is dependent on.

- **`templates` folder** contains the manifest template yaml files


# Helm — Kubernetes Package Manager

Helm is the package manager for Kubernetes. It's the best way to find, share, and use software built for Kubernetes.

---

## 1. Installation & Basic Commands

| Purpose | Command |
|---|---|
| Install Helm (Windows) | `winget install Helm.Helm` |
| Check installed version | `helm version` |
| List installed releases | `helm ls` |
| Show environment/config info | `helm env` |

---

## 2. Using Public Chart Repositories

```bash
# Add the Bitnami repo
helm repo add bitnami https://charts.bitnami.com/bitnami

# Install WordPress from the Bitnami repo, naming the release "happy-panda"
helm install happy-panda bitnami/wordpress

# Uninstall the release
helm uninstall happy-panda
```

---

## 3. Creating Custom Helm Charts

### 3.1 Common Chart (shared across all microservices)

```bash
cd D:\ramesh_workspace\microservice_workflow\section17\helm
helm create rameshbank-common
```

This creates the `rameshbank-common` folder with the standard chart structure:
`charts/`, `templates/`, `Chart.yaml`, `values.yaml`.

Customize it for shared use:
- Delete the default files in the `templates/` folder.
- Add custom template files: `configmap.yml`, `deployment.yml`, and `service.yml`.
- These two files (`deployment.yml` and `service.yml`) manage the deployment configuration for **all** microservices.

### 3.2 Per-Microservice Charts

Create a `rameshbank-services` folder, then create a chart and build dependencies for each microservice:

```bash
cd D:\ramesh_workspace\microservice_workflow\section17\helm\rameshbank-services

# Accounts
helm create accounts
cd accounts
helm dependency build
cd ..

# Loans
helm create loans
cd loans
helm dependency build
cd ..

# Cards
helm create cards
cd cards
helm dependency build
cd ..

# Config Server
helm create configserver
cd configserver
helm dependency build
cd ..

# Eureka Server
helm create eurekaserver
cd eurekaserver
helm dependency build
cd ..

# Gateway Server
helm create gatewayserver
cd gatewayserver
helm dependency build
cd ..

# Message Service
helm create message
cd message
helm dependency build
cd ..
```

---

## 4. Environment Charts (Dev, QA, Prod)

Create an `environments` folder to hold environment-specific charts:

```bash
cd D:\ramesh_workspace\microservice_workflow\section17\helm\environments

# Dev environment
helm create dev-env
cd dev-env
helm dependency build
cd ..

# QA environment
helm create qa-env
cd qa-env
helm dependency build
cd ..

# Prod environment
helm create prod-env
cd prod-env
helm dependency build
cd ..
```

### Preview Rendered Templates

Render the chart templates locally (without installing) to verify output:

```bash
cd D:\ramesh_workspace\microservice_workflow\section17\helm\environments\dev-env
helm template .
```

---

## 5. Setting Up the Keycloak Helm Chart

Reference: [Bitnami Charts – Keycloak](https://github.com/bitnami/charts/tree/main/bitnami/keycloak)

```bash
cd D:\ramesh_workspace\microservice_workflow\section17\helm

# Install Keycloak chart
helm install keycloak keycloak

# Build chart dependencies
cd keycloak
helm dependency build
```

