# 🏨 Spring PetClinic on EKS with ArgoCD, Prometheus & Grafana

## Overview

This project deploys **Spring PetClinic** on **AWS EKS** using **Terraform**, with **ArgoCD** for GitOps-based deployment and **Prometheus + Grafana** for monitoring.

---

## Table of Contents

1. [Project Structure](#project-structure)
2. [Prerequisites](#prerequisites)
3. [Setup](#setup)

   * [Terraform & EKS](#terraform--eks)
   * [ArgoCD](#argocd)
   * [Prometheus & Grafana](#prometheus--grafana)
4. [Deployment](#deployment)
5. [Accessing Services](#accessing-services)
6. [Monitoring](#monitoring)
7. [License](#license)

---

## Project Structure

```
spring-petclinic/
├── EKS_Terraform/            # Terraform configs for EKS cluster
│   ├── main.tf
│   ├── variables.tf
│   ├── deployment.yml
│   ├── svc.yaml
│   ├── spring-servicemonitor.yaml
│   └── prometheus-values.yaml
├── k8s/                      # Kubernetes manifests (optional, can be managed via ArgoCD)
├── src/                      # Spring PetClinic source code
├── Dockerfile                # Dockerfile for Spring App
├── docker-compose.yml
├── Jenkinsfile               # CI/CD pipeline
└── README.md
```

---

## Prerequisites

* AWS CLI & IAM permissions for EKS
* kubectl
* Helm
* Terraform
* ArgoCD (installed on cluster or locally)

---

## Setup

### Terraform & EKS

1. Initialize Terraform:

```bash
cd EKS_Terraform
terraform init
```

2. Plan and apply:

```bash
terraform plan
terraform apply
```

This creates the EKS cluster and deploys your Kubernetes manifests.

---

### ArgoCD

1. Port-forward ArgoCD server:

```bash
kubectl port-forward svc/argocd-server -n argocd 8080:443
```

2. Login (default admin password from secret):

```bash
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 --decode
```

3. Open browser at [https://localhost:8080](https://localhost:8080)

---

### Prometheus & Grafana

Installed via **Helm** in `prometheus` namespace.
Check pods:

```bash
kubectl get all -n prometheus
```

Port-forward Grafana:

```bash
kubectl port-forward svc/prometheus-grafana -n prometheus 3000:80
```

Login to Grafana:

* User: admin
* Password: from Prometheus Helm secret or default (check via `kubectl get secret`)

---

## Deployment

Spring PetClinic is deployed via **Deployment + Service**:

```bash
kubectl apply -f EKS_Terraform/deployment.yml
kubectl apply -f EKS_Terraform/svc.yaml
kubectl apply -f EKS_Terraform/spring-servicemonitor.yaml
```

---

## Accessing Services

* Spring PetClinic: `http://<LoadBalancer-IP>`
* Grafana: `http://localhost:3000` (port-forward)
* Prometheus: `http://localhost:9090` (port-forward)
* ArgoCD: `https://localhost:8080` (port-forward)

---

## Monitoring

* ServiceMonitor collects metrics from Spring PetClinic.
* Metrics visible in Grafana dashboards.

تحب أعملها لك كده؟
