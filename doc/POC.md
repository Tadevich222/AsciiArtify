# Proof of Concept (PoC): GitOps з ArgoCD на Kubernetes (k3d)

## Мета
Метою PoC є підтвердження можливості розгортання GitOps-процесу за допомогою **ArgoCD** у кластері Kubernetes, створеному на основі **k3d**.  
На цьому етапі демонструється робота веб-інтерфейсу ArgoCD та доступ команди до нього.

---

##  1. Підготовка середовища

### Необхідні інструменти:
- Docker
- kubectl
- k3d

Перевірка встановлення:
```bash
docker --version
kubectl version --client
k3d version
```

---

##  2. Створення Kubernetes-кластеру у k3d

```bash
k3d cluster create agrocd-cluster \
  --servers 1 \
  --agents 1 \
  -p "8080:80@loadbalancer" \
  -p "8443:443@loadbalancer"
```

Перевірити кластер:
```bash
kubectl get nodes
```

---

##  3. Встановлення ArgoCD

### Створення namespace:
```bash
kubectl create namespace argocd
```

### Встановлення ArgoCD:
```bash
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```

### Перевірка:
```bash
kubectl get pods -n argocd
```

---

##  4. Доступ до веб-інтерфейсу ArgoCD

### Переадресація порту:
```bash
kubectl port-forward svc/argocd-server -n argocd 8081:443
```

Інтерфейс доступний за адресою:

👉 https://127.0.0.1:8081

*(може бути попередження про самопідписаний сертифікат — це нормально)*

---

##  5. Отримання пароля адміністратора

```bash
kubectl get secret argocd-initial-admin-secret -n argocd -o jsonpath="{.data.password}" | base64 -d;echo
```

Логін: **admin**  
Пароль: *(виведе команда вище)*

---

##  6. Доступ команди (опціонально)
ArgoCD підтримує RBAC.  
ConfigMap:
```bash
kubectl edit configmap argocd-rbac-cm -n argocd
```

---
##  6. Демо запуску та фунціоналу ArgoCD

![Демо запуску ArgoCD](../src/media/chrome_61v3Be19Tj.gif)

---

## 🔗 Посилання
Офіційна документація:  
https://argo-cd.readthedocs.io/en/stable/