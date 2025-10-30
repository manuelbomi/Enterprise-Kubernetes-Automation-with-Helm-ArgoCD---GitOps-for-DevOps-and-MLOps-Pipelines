#  Enterprise Kubernetes GitOps Pipeline — Helm + ArgoCD for DevOps & MLOps Automation

### Project Description

This repository provides a **complete enterprise-grade GitOps automation workflow** built on top of Kubernetes.  
It demonstrates how to deploy, scale, and manage both **applications** and **machine learning models** using:
- **Helm** — for Kubernetes packaging and version control  
- **ArgoCD** — for declarative GitOps-based continuous delivery  
- **GitHub Actions** — for CI/CD automation  
- **Kubernetes** — as the scalable orchestration backbone  

The setup follows industry best practices for **DevOps**, **MLOps**, and **GitOps**, enabling:
- Seamless deployments from code to cluster  
- Automated versioning, rollbacks, and environment promotion  
- Continuous delivery pipelines for microservices and ML models  
- Reproducible infrastructure-as-code for enterprise environments  

---


## Background Discussion


#### As your Kubernetes environment grows, managing many YAML files manually becomes cumbersome.  

#### That is  where **Helm** comes in.

#### Helm is often called the **“package manager for Kubernetes.”**  

#### Helm lets you bundle all your YAML configurations (Deployments, Services, Ingress, etc.) into a single **Helm chart** that can be versioned, parameterized, and deployed consistently across environments (Dev, Staging, Production).

---

###  WHY USE HELM?

| Feature | Benefit |
|----------|----------|
| **Templating** | Avoids hardcoding values (e.g., image tags, ports). |
| **Version Control** | Each deployment is versioned and rollbacks are easy. |
| **Consistency** | Guarantees uniform deployment across clusters. |
| **CI/CD Friendly** | Works seamlessly with Jenkins, GitHub Actions, or ArgoCD. |



---

##  STEP 1 — INSTALL HELM

###  Windows
```bash
choco install kubernetes-helm


 macOS

```python
brew install helm
```

Linux

```python

curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3
chmod 700 get_helm.sh
./get_helm.sh

```

Verify installation:

```python
helm version
```

## STEP 2 — CREATE A HELM CHART

#### Helm organizes apps in charts, each containing templates and configuration files.

#### Create a new chart:

```python
helm create nginx-chart
```

This command generates a folder structure like:

```python
nginx-chart/
├── Chart.yaml
├── values.yaml
├── templates/
│   ├── deployment.yaml
│   ├── service.yaml
│   ├── ingress.yaml
│   └── _helpers.tpl
```

> [!NOTE]
> The example uses NGINX, but the folder structure is a reusable blueprint for enterprise Kubernetes GitOps repos.
>
> # How to Understand the Structure

| Folder | Purpose | Example Content |
|--------|---------|-----------------|
| **nginx-chart/** | A Helm chart for any frontend/web app | deployment.yaml, values.yaml, service.yaml |
| **ml-inference-chart/** | A Helm chart for any ML model or inference service | ML API deployment & service files |
| **.github/workflows/** | CI/CD automation pipelines | GitHub Actions for build/deploy |
| **README.md** | Documentation | Your Kubernetes + Helm + ArgoCD guide |

#### Rename these folders for your real projects

#### If your services aren't NGINX or ML inference, rename like this:

#### Example enterprise naming:

```python

 | enterprise-k8s-gitops-pipeline
 ├── api-service-chart/
 ├── frontend-ui-chart/
 ├── model-serving-chart/
 ├── data-pipeline-chart/
 ├── .github/workflows/
 └── README.md

```

#### Or if you are doing MLOps:

```python
| ai-platform-k8s-gitops
 ├── feature-store-chart/
 ├── model-training-chart/
 ├── model-serving-chart/
 ├── monitoring-stack-chart/  # Prometheus/Grafana
 ├── .github/workflows/
 └── README.md

```

### <ins>Key Point</ins>

- Each folder = one deployable service packaged as a Helm chart.

- You can add more charts as your enterprise application grows:

```python
| Example Service          | Chart Name            |
| ------------------------ | --------------------- |
| API Backend              | `backend-api-chart/`  |
| Web Frontend             | `web-ui-chart/`       |
| Redis Cache              | `redis-chart/`        |
| Postgres DB              | `postgres-chart/`     |
| ML Model Inference       | `ml-inference-chart/` |
| Kafka Broker             | `kafka-chart/`        |
| Airflow ML Orchestration | `airflow-chart/`      |
```

---

## STEP 3 — EDIT THE HELM TEMPLATES

#### Open templates/deployment.yaml and make sure it contains:

```python
apiVersion: apps/v1
kind: Deployment
metadata:
  name: {{ include "nginx-chart.fullname" . }}
spec:
  replicas: {{ .Values.replicaCount }}
  selector:
    matchLabels:
      app: {{ include "nginx-chart.name" . }}
  template:
    metadata:
      labels:
        app: {{ include "nginx-chart.name" . }}
    spec:
      containers:
      - name: nginx
        image: "{{ .Values.image.repository }}:{{ .Values.image.tag }}"
        ports:
        - containerPort: {{ .Values.service.port }}
```

## STEP 4 — CONFIGURE VALUES (Dynamic Parameters)

#### Edit values.yaml to define variables used by templates:

```python
replicaCount: 2

image:
  repository: nginx
  tag: latest
  pullPolicy: IfNotPresent

service:
  type: NodePort
  port: 80
  nodePort: 30001
```

#### Now your Deployment and Service use dynamic values from values.yaml.

#### You can change replicas, image tags, or ports without touching YAML templates — ideal for CI/CD.

## STEP 5 — DEPLOY USING HELM

#### Package and install your Helm chart:

```python
helm install my-nginx nginx-chart/
```

#### Helm will render your YAML templates and deploy them to Kubernetes.

#### Check resources:

```python
kubectl get all
```

#### You will see your deployment and service created — just like before, but now fully managed through Helm.


## STEP 6 — UPDATE & ROLLBACK

#### When you need to make changes (e.g., new image version):

#### Edit values.yaml:

```python
image:
  repository: nginx
  tag: 1.27
```

#### Then upgrade:

```python
helm upgrade my-nginx nginx-chart/
```

#### Helm tracks revision history automatically. 

#### If something goes wrong, rollback instantly:

```python
helm rollback my-nginx 1
```

## STEP 7 — CI/CD INTEGRATION EXAMPLE (GitHub Actions)

#### Add a GitHub workflow .github/workflows/helm-deploy.yaml:

```python
name: Helm Deploy

on:
  push:
    branches:
      - main

jobs:
  deploy:
    runs-on: ubuntu-latest

    steps:
    - name: Checkout repository
      uses: actions/checkout@v4

    - name: Set up kubectl
      uses: azure/setup-kubectl@v3
      with:
        version: 'latest'

    - name: Set up Helm
      uses: azure/setup-helm@v3
      with:
        version: 'latest'

    - name: Deploy Helm Chart
      run: |
        helm upgrade --install my-nginx ./nginx-chart --wait
```

#### This workflow automatically:

- Builds and packages your Helm chart.

- Deploys it on every commit to main.

- Waits until all pods are healthy before finishing.

---

## HELM FOR MLOPS PIPELINES

#### Using Helm is not limited to web apps — it is widely used for machine learning pipelines too.

#### Example: Deploying an ML inference service

```python
helm create ml-inference-chart
```

#### Then modify values.yaml:

```python
image:
  repository: yourdockerhub/ml-inference
  tag: latest
service:
  type: LoadBalancer
  port: 5000
```

#### Deploy:

```python
helm install inference ml-inference-chart/
```

#### Now, your trained model can be deployed as a versioned, scalable, reproducible Helm release — ideal for A/B testing models, rolling upgrades, or canary releases in production.

### ENTERPRISE BENEFITS OF USING HELM

| Area | Advantage |
|------|-----------|
| **DevOps Automation** | Simplifies CI/CD deployments across multiple environments. |
| **Configuration Management** | Centralizes environment-specific settings in values.yaml. |
| **Scalability** | Supports multiple replicas and autoscaling templates out of the box. |
| **Disaster Recovery** | Instant rollbacks and version control for all Kubernetes resources. |
| **MLOps Workflows** | Enables reproducible deployments for data preprocessing, model training, and inference services. |


---

---

##  ARGOCD GITOPS AUTOMATION — CONTINUOUS DELIVERY WITH HELM

After using Helm to package and manage your Kubernetes apps, the next step toward **full enterprise automation** is **GitOps** — a methodology where *Git becomes the single source of truth* for your deployments.

This is implemented using **ArgoCD** — a declarative, GitOps-based continuous delivery tool for Kubernetes.

---

##  WHAT IS GITOPS?

**GitOps** means:
- Your Git repository (e.g., GitHub) stores your app manifests (YAML or Helm).
- Any change pushed to Git automatically updates your Kubernetes cluster.
- The cluster *self-corrects* if it drifts from the declared state in Git.

ArgoCD continuously monitors your repo, detects changes, and applies them automatically.


---

##  WHY USE ARGOCD?

| Feature | Description |
|----------|--------------|
| **Continuous Delivery** | Automatically syncs Kubernetes clusters with your Git repo. |
| **Declarative Management** | Everything (apps, services, Helm charts) is defined in Git. |
| **Security & Auditability** | Every deployment is version-controlled and reviewable. |
| **Multi-Cluster Support** | Manage many Kubernetes environments from one ArgoCD UI. |
| **Visual Dashboard** | View app health, sync status, and deployment history in real time. |

---

##  STEP 1 — INSTALL ARGOCD IN YOUR CLUSTER

Run the following commands in your Minikube or Kubernetes cluster:

```bash
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml

### Wait until all ArgoCD pods are running:

```python
kubectl get pods -n argocd
```

#### You should see output like:

```python
argocd-server-7f7d8f7d45-lczqw     1/1     Running   0   2m
argocd-repo-server-6c4754b6d5-gqf5x 1/1     Running   0   2m
```

## STEP 2 — ACCESS THE ARGOCD DASHBOARD

#### Forward the ArgoCD UI port:

```python
kubectl port-forward svc/argocd-server -n argocd 8080:443

```

#### Now visit:

**https://localhost:8080**

#### Get the initial admin password:

```python
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d && echo
```

### Login using:

#### Username: admin

#### Password: (the one you just retrieved)


## STEP 3 — CONNECT ARGOCD TO YOUR GITHUB REPOSITORY

#### In the ArgoCD dashboard:

- Click Settings → Repositories → Connect Repo.

- Add your GitHub repository URL where your Helm chart or YAML files live.

- You can connect via HTTPS or SSH.

Example repository layout:

```python
/my-enterprise-k8s-repo
 ├── nginx-chart/
 ├── ml-inference-chart/
 ├── .github/workflows/
 └── README.md

```

## STEP 4 — CREATE AN APPLICATION IN ARGOCD

#### You can do this from the dashboard or via a YAML manifest.

#### Example nginx-argocd-app.yaml:

```python
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: nginx-helm-app
  namespace: argocd
spec:
  project: default
  source:
    repoURL: 'https://github.com/yourusername/my-enterprise-k8s-repo.git'
    targetRevision: main
    path: nginx-chart
    helm:
      valueFiles:
        - values.yaml
  destination:
    server: 'https://kubernetes.default.svc'
    namespace: default
  syncPolicy:
    automated:
      prune: true
      selfHeal: true
```

#### Apply the app manifest:

```python
kubectl apply -f nginx-argocd-app.yaml
```

## STEP 5 — WATCH GITOPS IN ACTION

#### Now, every time you push changes to your Helm chart or YAML files on GitHub:

- ArgoCD detects the change.

- It automatically pulls the new config.

- It updates the Kubernetes cluster to match the new desired state.

#### You can visually see this happening in the ArgoCD dashboard under your application’s name:

- Healthy: All pods running as expected.

- Synced: Cluster matches the Git repo.

- OutOfSync: Git repo has new changes pending sync.


## OPTIONAL: AUTO-SYNC WITH HELM RELEASES

#### If you use Helm charts, ArgoCD can automatically manage version upgrades.

#### Example:

You bump the image tag in values.yaml:

```python
image:
  repository: nginx
  tag: 1.27.0
```

#### Commit and push → ArgoCD notices → Kubernetes auto-updates → Helm performs the upgrade under the hood.

#### No manual kubectl apply or helm upgrade required.

---

## ARGOCD + HELM + GITHUB ACTIONS — ENTERPRISE PIPELINE OVERVIEW

#### Below is a simplified diagram of the end-to-end DevOps + MLOps pipeline:

```python

+---------------------+
|    Developer Push   |
|   (GitHub Commit)   |
+---------+-----------+
          |
          v
+---------------------------+
|  GitHub Actions CI Build  |
|  (Tests, Docker Build)    |
+-------------+-------------+
              |
              v
+---------------------------+
|    Helm Chart Updates     |
|   (values.yaml changes)   |
+-------------+-------------+
              |
              v
+---------------------------+
|   ArgoCD GitOps Syncs     |
|   (Auto Deploy to K8s)    |
+-------------+-------------+
              |
              v
+---------------------------+
|   Kubernetes Cluster      |
| (Pods, Services, Autoscale)|
+---------------------------+

```

### This workflow delivers:

- Continuous Integration via GitHub Actions
-  Continuous Delivery via ArgoCD
- Declarative Config Management via Helm
- Observability & Auto-Healing via Kubernetes
- 
---

## ENTERPRISE MLOPS INTEGRATION

#### For MLOps pipelines, the same pattern applies:

- Training Jobs (Kubeflow, ZenML, or Airflow) push new model artifacts to a registry.

- Helm values.yaml updates the new model version or Docker image tag.

- ArgoCD detects the change and automatically deploys the new model pod.

- Prometheus/Grafana monitors inference latency and health.

#### You get an end-to-end automated model lifecycle — from code → build → deploy → monitor.

---
  
### CONCLUSION — COMPLETE ENTERPRISE GITOPS PIPELINE


#### By integrating ArgoCD with Helm, your Kubernetes environment now achieves:


#### Together, Kubernetes + Helm + ArgoCD form the gold standard of enterprise DevOps/MLOps automation — scalable, auditable, and fully declarative.

---

### ENTERPRISE BENEFITS OF USING ARGOCD

| Capability | Description |
|------------|-------------|
| **GitOps Automation** | Full synchronization between Git and cluster state. |
| **Continuous Deployment** | Auto-deploy every commit with rollback safety. |
| **Helm Versioning** | Easy upgrades and rollbacks of Helm-based apps. |
| **Multi-Cluster Control** | Manage Dev, Staging, and Production clusters centrally. |
| **MLOps Alignment** | Supports automated model retraining and redeployment. |


### Thank you for reading
---

### **AUTHOR'S BACKGROUND**
### Author's Name:  Emmanuel Oyekanlu
```
Skillset:   I have experience spanning several years in data science, developing scalable enterprise data pipelines,
enterprise solution architecture, architecting enterprise systems data and AI applications,
software and AI solution design and deployments, data engineering, high performance computing (GPU, CUDA), machine learning,
NLP, Agentic-AI and LLM applications as well as deploying scalable solutions (apps) on-prem and in the cloud.

I can be reached through: manuelbomi@yahoo.com

Website:  http://emmanueloyekanlu.com/
Publications:  https://scholar.google.com/citations?user=S-jTMfkAAAAJ&hl=en
LinkedIn:  https://www.linkedin.com/in/emmanuel-oyekanlu-6ba98616
Github:  https://github.com/manuelbomi

```
[![Icons](https://skillicons.dev/icons?i=aws,azure,gcp,scala,mongodb,redis,cassandra,kafka,anaconda,matlab,nodejs,django,py,c,anaconda,git,github,mysql,docker,kubernetes&theme=dark)](https://skillicons.dev)
