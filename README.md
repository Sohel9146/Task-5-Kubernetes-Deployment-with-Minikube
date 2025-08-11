# 🚀 Kubernetes Deployment with Minikube (Task 5)

![Kubernetes](https://img.shields.io/badge/Kubernetes-v1.28-blue?logo=kubernetes&logoColor=white)
![Minikube](https://img.shields.io/badge/Minikube-v1.36.0-orange?logo=google-cloud&logoColor=white)
![Docker](https://img.shields.io/badge/Docker-20.10-blue?logo=docker&logoColor=white)


---

## 📌 Project Overview
This project demonstrates deploying an **Nginx application** on Kubernetes using **Minikube** with a **NodePort Service**, including scaling, rolling updates, and accessing it externally.

---

## 📊 Architecture Diagram

```
+-----------------------------+
|        Client Browser       |
|  (Local PC / External IP)   |
+-------------+---------------+
              |
              v
+-----------------------------+
|  NodePort Service (30007)   |
+-------------+---------------+
              |
              v
+-----------------------------+
|      Nginx Deployment       |
|   (2–4 Replicas / Pods)     |
+-------------+---------------+
              |
              v
+-----------------------------+
|       Minikube Cluster      |
|  (Single-node Kubernetes)   |
+-----------------------------+
```

---

## 📋 Table of Contents
1. [Prerequisites](#-prerequisites)
2. [Installation](#-installation)
3. [Start Minikube](#-start-minikube)
4. [Create Kubernetes YAMLs](#-create-kubernetes-yamls)
5. [Deploy to Kubernetes](#-deploy-to-kubernetes)
6. [Verify Deployment](#-verify-deployment)
7. [Scaling the Deployment](#-scaling-the-deployment)
8. [Rolling Updates & Rollbacks](#-rolling-updates--rollbacks)
9. [Access Application from Outside](#-access-application-from-outside)
10. [Cleanup](#-cleanup)
11. [Screenshots](#-screenshots)

---

## 🛠 Prerequisites
- **CentOS 9** or similar Linux
- **Docker** installed & running
- **kubectl** installed
- **Minikube** installed

---

## 📥 Installation

```bash
# Install Docker
sudo yum install -y docker
sudo systemctl enable --now docker

# Install kubectl
curl -LO "https://storage.googleapis.com/kubernetes-release/release/$(curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt)/bin/linux/amd64/kubectl"
chmod +x kubectl
sudo mv kubectl /usr/local/bin/

# Install Minikube
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
sudo install minikube-linux-amd64 /usr/local/bin/minikube
```

---

## ▶ Start Minikube

```bash
minikube start --driver=docker
minikube status
kubectl get nodes
```

📸![Screenshot #1 – Minikube start output](https://github.com/Sohel9146/Task-5-Kubernetes-Deployment-with-Minikube/blob/main/screenshots/Minikube%20Start.png?raw=true) 
📸![Screenshot #2 – Kubectl get nodes](https://github.com/Sohel9146/Task-5-Kubernetes-Deployment-with-Minikube/blob/main/screenshots/5%20-%20Get%20Nodes.png?raw=true)

---

## 📝 Create Kubernetes YAMLs

### **1. Deployment**
`deployment.yaml`
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  labels:
    app: nginx
spec:
  replicas: 2
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.24
        ports:
        - containerPort: 80
```

### **2. Service**
`service.yaml`
```yaml
apiVersion: v1
kind: Service
metadata:
  name: nginx-service
spec:
  type: NodePort
  selector:
    app: nginx
  ports:
  - port: 80
    targetPort: 80
    nodePort: 30007
```

---

## 🚀 Deploy to Kubernetes
```bash
kubectl apply -f deployment.yaml
kubectl apply -f service.yaml
```

📸![Screenshot #3 – kubectl get pods -o wide](https://github.com/Sohel9146/Task-5-Kubernetes-Deployment-with-Minikube/blob/main/screenshots/6%20-%20Get%20Pods%20-o%20Wide.png?raw=true)  
📸![Screenshot #4 – kubectl get svc](https://github.com/Sohel9146/Task-5-Kubernetes-Deployment-with-Minikube/blob/main/screenshots/4%20-%20Get%20Service.png?raw=true)

---

## ✅ Verify Deployment
```bash
minikube service nginx-service --url
```
OR  
```bash
kubectl port-forward --address 0.0.0.0 svc/nginx-service 8080:80
```

📸![Screenshot #5 – Browser view of the App](https://github.com/Sohel9146/Task-5-Kubernetes-Deployment-with-Minikube/blob/main/screenshots/App%20Running%20On%20Browser.png?raw=true)

---

## 📈 Scaling the Deployment
```bash
kubectl scale deployment/nginx-deployment --replicas=4
kubectl get pods
```

📸![Screenshot #6 – kubectl get pods After Scaling](https://github.com/Sohel9146/Task-5-Kubernetes-Deployment-with-Minikube/blob/main/screenshots/8%20-%20Scale%20Apps.png?raw=true)

---

## 🔄 Rolling Updates & Rollbacks
```bash
kubectl set image deployment/nginx-deployment nginx=nginx:1.25.0 --record
kubectl rollout status deployment/nginx-deployment
kubectl rollout history deployment/nginx-deployment

# Rollback if needed
kubectl rollout undo deployment/nginx-deployment
```

📸![Screenshot #7 – Rolling update out](https://github.com/Sohel9146/Task-5-Kubernetes-Deployment-with-Minikube/blob/main/screenshots/9%20-%20Rollout%20Status%20and%20Rollout%20History.png?raw=true)  
📸![Screenshot #8 – Rollback output](https://github.com/Sohel9146/Task-5-Kubernetes-Deployment-with-Minikube/blob/main/screenshots/Rollback.png?raw=true)

---

## 🌐 Access Application from Outside

### **1. Get Minikube IP**
```bash
minikube ip
```

### **2. Open Port in Firewall**
```bash
sudo firewall-cmd --add-port=30007/tcp --permanent
sudo firewall-cmd --reload
```

### **3. Access via Browser**
```
http://<server-public-ip>:30007
```

📸![Screenshot #9 – External Browser Access](https://github.com/Sohel9146/Task-5-Kubernetes-Deployment-with-Minikube/blob/main/screenshots/App%20Running%20On%20Browser.png?raw=true)

---

## 🧹 Cleanup
```bash
kubectl delete -f service.yaml
kubectl delete -f deployment.yaml
minikube stop
minikube delete
```

---

## 📷 Screenshots
> All screenshots should be stored in the `screenshots/` folder.

1. Create Deployment  
2. Get Deployment  
3. Create Service  
4. Get Service  
5. Get nodes  
6. Get Pods -o Wide  
7. Port Forwarding 
8. Scale Apps
9. Rollout Status and Rollout History
10. App Running on Browser
11. Describe Pods
12. Minikube Installation
13. Minikube Start
14. Minikube Status
15. Pod Logs
16. Rollback

---

## 👤 Author
**Sohel Shaikh**  
📧 Email: suhailshaikh7866@gmail.com  
🔗 [Naukri](https://www.naukri.com/mnjuser/profile) | [GitHub](https://github.com/Sohel9146)
