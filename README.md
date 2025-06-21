# 🚀 Deploy Dockerized Web App using Ansible Playbook on Kubernetes Cluster

This project demonstrates how to **automate the deployment of a Dockerized web application** on a **Kubernetes cluster (Minikube)** using **Ansible**. The entire lifecycle — from environment setup to app deployment and troubleshooting — is covered using simple scripts and automation tools.

---


## 📁 Project Structure

| File/Folder         | Description                                |
|---------------------|--------------------------------------------|
| `Dockerfile`        | Defines container image for web app        |
| `deployment.yml.j2` | Jinja2 template for K8s Deployment          |
| `service.yml.j2`    | Jinja2 template for K8s Service             |
| `vars.yml`          | Variables used in the templates             |
| `playbook.yml`      | Ansible Playbook for image build + deploy  |


## 🛠️ Step-by-Step Setup & Workflow

### 1️⃣ Environment Setup

✅ Installed on an **EC2 (Amazon Linux 2023)** instance:

- Docker
- kubectl
- Minikube
- Ansible

🟢 Enable Docker:
```bash
sudo systemctl start docker && sudo systemctl enable docker
```

### 2️⃣ Configure Minikube Docker Daemon
🐳 Use Minikube’s Docker environment so your image can be used inside the cluster:
```bash
eval $(minikube docker-env)
```

### 3️⃣ Create Simple Web App
🧱 Developed a minimal Nginx-based web app and created a basic Dockerfile:

dockerfile
```bash
FROM nginx
COPY index.html /usr/share/nginx/html/index.html
```

### 4️⃣ Kubernetes YAML Templates with Jinja2
📄 Created templated K8s manifests:

`deployment.yml.j2`

`service.yml.j2`

🧩 These templates use variables like:

`app_name`

`image`

`container_port`

📂 All values were moved into a clean and reusable vars.yml file.

### 5️⃣ Write Ansible Playbook
🎯 Tasks include:

Building the Docker image with community.docker.docker_image

Applying K8s YAML templates with kubernetes.core.k8s

💡 Run everything locally:

```bash
hosts: localhost
connection: local
```

### 6️⃣ Run Ansible Playbook
🏃 Execute the deployment with:

```bash
ansible-playbook playbook.yml
```

### 7️⃣ Deploy to Minikube
☸️ Kubernetes created:

A Deployment and

A Service using the local Docker image

### 8️⃣ 🛑 Faced ImagePullBackOff Issue
❌ Error:


`ImagePullBackOff: requested access to the resource is denied`  
🔍 Cause: Kubernetes tried to pull the image from DockerHub.

✅ Fix:
```bash
eval $(minikube docker-env)
docker build -t mywebapp:latest .
```

### 9️⃣ Expose App to the Web 🌐
🛑 minikube service IP (192.168.49.2) doesn’t work on EC2 (internal-only IP).

✅ Use port forwarding:

```bash
kubectl port-forward --address 0.0.0.0 service/mywebapp-service 8081:80
```
🌐 Open in browser:

```bash
http://<EC2-PUBLIC-IP>:8081
```
## ⚔️ Challenges Faced & Solutions

| ❌ Challenge                                  | ✅ Solution                                                              |
|----------------------------------------------|--------------------------------------------------------------------------|
| ImagePullBackOff (access denied)             | Built image inside Minikube using `eval $(minikube docker-env)`         |
| Kubernetes tried pulling from DockerHub      | Used local image with `docker build` inside Minikube Docker environment |
| App not accessible via `minikube service`    | Used `kubectl port-forward --address 0.0.0.0` for external access       |
| Template parsing failed (tab character issue)| Replaced `\t` with proper spaces in `.j2` templates                     |
| Ansible failed due to missing modules        | Installed using `pip3 install openshift kubernetes`                    |



## 💡 Key Concepts Demonstrated  
✅ Docker image build automation

✅ Kubernetes deployment via Ansible

✅ Jinja2 templating in Kubernetes manifests

✅ Clean config separation via vars.yml

✅ Port-forwarding to access apps on EC2

✅ Real-world troubleshooting: ImagePullBackOff, networking issues
