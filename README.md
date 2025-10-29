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
