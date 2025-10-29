---

##  HELM EXAMPLE — PACKAGING ENTERPRISE DEPLOYMENTS

As your Kubernetes environment grows, managing many YAML files manually becomes cumbersome.  
That’s where **Helm** comes in.

Helm is often called the **“package manager for Kubernetes.”**  
It lets you bundle all your YAML configurations (Deployments, Services, Ingress, etc.) into a single **Helm chart** that can be versioned, parameterized, and deployed consistently across environments (Dev, Staging, Production).

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
