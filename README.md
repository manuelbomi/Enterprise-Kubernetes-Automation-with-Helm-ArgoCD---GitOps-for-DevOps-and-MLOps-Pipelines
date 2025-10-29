---

## ⚓ HELM EXAMPLE — PACKAGING ENTERPRISE DEPLOYMENTS

As your Kubernetes environment grows, managing many YAML files manually becomes cumbersome.  
That’s where **Helm** comes in.

Helm is often called the **“package manager for Kubernetes.”**  
It lets you bundle all your YAML configurations (Deployments, Services, Ingress, etc.) into a single **Helm chart** that can be versioned, parameterized, and deployed consistently across environments (Dev, Staging, Production).

---

### 🧩 WHY USE HELM?

| Feature | Benefit |
|----------|----------|
| **Templating** | Avoids hardcoding values (e.g., image tags, ports). |
| **Version Control** | Each deployment is versioned and rollbacks are easy. |
| **Consistency** | Guarantees uniform deployment across clusters. |
| **CI/CD Friendly** | Works seamlessly with Jenkins, GitHub Actions, or ArgoCD. |

---

## 🧱 STEP 1 — INSTALL HELM

### 🪟 Windows
```bash
choco install kubernetes-helm
