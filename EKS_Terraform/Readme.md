# Spring PetClinic + ArgoCD + Prometheus/Grafana on EKS

This project is a full-stack setup including:
- **Spring PetClinic Application** (Java/Spring Boot)
- **GitOps with ArgoCD** for application deployment and management
- **Monitoring** using Prometheus and Grafana
- **Infrastructure as Code** with Terraform on EKS

---

## 🏗 Architecture

High-level architecture of the project and its resources:

![Architecture](screens/all-a.png)
![Architecture](screens/all-b.png)
![Architecture](screens/all-c.png)

---

## 🔌 Port Forwarding

Access services internally via port forwarding:

![Port Forward](screens/portforward.png)

---

## 🐱 Spring PetClinic Application

Application interface after deployment on the cluster:

![App](screens/app.png)

---

## 🚀 ArgoCD

### ArgoCD Dashboard

Manage applications and GitOps deployment via ArgoCD:

![ArgoCD 1](screens/argo-a.png)
![ArgoCD 2](screens/argo-b.png)

**Sync Policy Options**:
- **Manual**: Requires manual sync for changes
- **Automated**: Auto-syncs when Git repository changes

---

## 📊 Prometheus & Grafana

### Grafana Dashboard

Visualize metrics and dashboards:

![Grafana](screens/grafana.png)

### Prometheus Targets

Monitor application and cluster targets:

![Prometheus Target 1](screens/target1.png)
![Prometheus Target 2](screens/target2.png)

**Accessing Dashboards via Port Forwarding**:

```bash
kubectl port-forward svc/prometheus-grafana 3000:80 -n prometheus
kubectl port-forward svc/prometheus-kube-prometheus-prometheus 9090:9090 -n prometheus
````

**Default Grafana password**:

```bash
kubectl get secret prometheus-grafana -n prometheus -o jsonpath="{.data.admin-password}" | base64 --decode
```

---

## 🗂 Terraform (EKS)

All infrastructure files are inside:

```
EKS_Terraform/
```

### Key Files:

* `main.tf` – VPC, Subnets, EKS Cluster configuration
* `deployment.yml` – Spring PetClinic Deployment
* `svc.yaml` – Spring PetClinic Service
* `prometheus-values.yaml` – Helm values for Prometheus/Grafana
* `spring-servicemonitor.yaml` – ServiceMonitor to monitor Spring app
* `.terraform/` – Local provider plugins (ignored in Git)
* `terraform.tfstate` & `terraform.tfstate.backup` – Terraform state files (do not commit)

---

## ⚡ Usage

### 1️⃣ Deploy the EKS Cluster with Terraform

```bash
cd EKS_Terraform
terraform init
terraform apply
```

### 2️⃣ Deploy Spring PetClinic

```bash
kubectl apply -f deployment.yml
kubectl apply -f svc.yaml
```

### 3️⃣ Install Prometheus/Grafana via Helm

```bash
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo update

helm upgrade --install prometheus prometheus-community/kube-prometheus-stack \
  --namespace prometheus --create-namespace \
  -f prometheus-values.yaml
```

### 4️⃣ Manage Applications with ArgoCD

```bash
argocd login <ARGOCD_SERVER> --username admin --password <PASSWORD>

argocd app create spring-application \
  --repo <GIT_REPO_URL> \
  --path EKS_Terraform \
  --dest-namespace default \
  --dest-server https://kubernetes.default.svc \
  --sync-policy automated

# Sync the application
argocd app sync spring-application
```

---

## 🔧 Notes & Best Practices

* Avoid committing `.terraform/` and `terraform.tfstate` files due to size.
* ServiceMonitor namespace must match Prometheus Operator namespace.
* Helm charts allow customization via `prometheus-values.yaml`.
* Prerequisites:

  * `kubectl`
  * `helm` >= 3
  * `terraform` >= 1.0
  * AWS CLI configured
  * ArgoCD CLI (`argocd`)
* To add new microservices, create a new Deployment/Service/ServiceMonitor and link to ArgoCD.
* Screenshots for reference are inside `screens/` folder.

---

## 📁 GitHub Repo Structure

```
EKS_Terraform/
screens/
README.md
```
