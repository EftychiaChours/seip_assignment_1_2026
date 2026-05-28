# SEIP Assignment 1 - Cloud-Native Echo API Application

This repository contains a containerized Node.js application deployed on a local Kubernetes cluster using Minikube. The project features a fully automated CI/CD pipeline via GitHub Actions and secure infrastructure configuration.

---

## Prerequisites

Before running the project, ensure you have the following tools installed and configured on your local machine:
* [Git](https://git-scm.com/)
* [Docker Desktop](https://www.docker.com/products/docker-desktop/) (Running with the Docker Engine active)
* [Minikube](https://minikube.sigs.k8s.io/docs/start/?arch=%2Fwindows%2Fx86-64%2Fstable%2F.exe+download)
* [kubectl](https://kubernetes.io/docs/tasks/tools/)

---
## Step-by-Step Setup Guide

### 1. Cloning the Repository

First, clone the repository to your local machine and navigate into the project directory:
```bash
git clone https://github.com/EftychiaChours/seip_assignment_1_2026.git
cd seip_assignment_1_2026
```

### 2. Spinning up Minikube

Start your local Kubernetes cluster using the Docker driver to ensure clean network isolation:
```bash
minikube start --driver=docker
```
Wait until you see the success message "🏄  Done! kubectl is now configured to use "minikube" cluster and "default" namespace by default"

### 3. Applying the manifests sequentially

The infrastructure manifests are located inside the k8s/ directory.
```bash
kubectl apply -f k8s/
```
This will create the following:
1. **ConfigMap** (configmap.yaml) - (WELCOME_MESSAGE, NODE_ENV).
2. **Secret** (secret.yaml) - (API_SECRET_KEY)
3. **Deployment** (deployment.yaml) - (Provisions 3 replicas of the application, enforces resource limits, configures self-healing.)
4. **Service** (service.yaml) - (Provisions a ClusterIP network service.)

You can verify that all resources are active and running by executing:
```bash
kubectl get all -n default
```

### 4. Interacting with the endpoints

Since the Service is ClusterIP, you must create a network port-forward to expose it to your host machine's browser.
Run the following command in your terminal:
```bash
kubectl port-forward --address localhost service/echo-api-service 8080:80
```
Note: Keep this terminal open.

Open your browser and navigate to the following verified endpoints:
- URL: http://localhost:8080/ (Displays the welcome message from ConfigMap)
- URL: http://localhost:8080/secure-config (Displays an "Authorized" status along with the masked string suffix of API_SECRET_KEY from Secret)

## Additional helpful commands

When you are done testing the application endpoints in your browser, you should safely close the network tunnel by pressing 'Ctrl + C' in your terminal window.
Then, stop the local Kubernetes cluster safely without losing your data:
```bash
minikube stop
```

### Resetting the Cluster
If the Kubernetes cluster freezes for any reason or throws errors, perform a clean hard reset:
1. Purge the frozen cluster and clear all stuck network bridges
```bash
minikube delete
```
2. Restart the Docker Desktop application from its GUI.
3. Spin up a fresh local Kubernetes cluster
```bash
minikube start --driver=docker
```
4. Re-apply all infrastructure manifests sequentially
```bash
kubectl apply -f k8s/
```

