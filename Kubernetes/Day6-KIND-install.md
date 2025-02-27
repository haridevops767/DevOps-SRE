# **How to install kubernetes using KIND On Ubuntu**

## **Install Kubernetes using KIND on Ubuntu 22.04**  

KIND (Kubernetes IN Docker) is a tool for running Kubernetes clusters using Docker containers instead of VMs. It's lightweight and great for testing and development.

---

## **Prerequisites**
Before installing KIND, ensure you have:
1. **Ubuntu 22.04 Server**
2. **Docker installed**
3. **kubectl installed**
4. **At least 2 vCPUs and 2GB RAM** (4GB recommended)
5. **User with sudo privileges**

---

## **Step 1: Update Your System**
Run the following command to update and upgrade your system:
```bash
sudo apt update && sudo apt upgrade -y
```

---

## **Step 2: Install Docker**
KIND requires Docker to run Kubernetes clusters as containers.

 Install Docker:
```bash
sudo apt install -y docker.io
```

 Enable and start Docker:
```bash
sudo systemctl enable --now docker
```

 Verify Docker installation:
```bash
docker --version
```
**Sample Outpt**

![image](https://github.com/user-attachments/assets/79aafe24-1a6d-4154-b676-0483801fde35)

 Add your user to the `docker` group (optional, to run Docker without sudo):
```bash
sudo usermod -aG docker $USER
newgrp docker
```

---

## **Step 3: Install kubectl (Kubernetes CLI)**
KIND requires `kubectl` to interact with the Kubernetes cluster.

 Download and install `kubectl`:
```bash
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
```
**Sample Outpt**

![image](https://github.com/user-attachments/assets/2182915d-a93f-4e49-91cc-496399c3b250)

 Make it executable:
```bash
chmod +x kubectl
sudo mv kubectl /usr/local/bin/
```

 Verify installation:
```bash
kubectl version --client
```
**Sample Outpt**

![image](https://github.com/user-attachments/assets/77e62a67-c9f0-4acb-91d6-a59acadb276e)

---

## **Step 4: Install KIND**
 Download and install KIND:
```bash
curl -Lo ./kind "https://kind.sigs.k8s.io/dl/latest/kind-linux-amd64"
```
**Sample Outpt**

![image](https://github.com/user-attachments/assets/3db7a0c7-ea61-4031-83d7-1fb1212d859a)

 Make it executable:
```bash
chmod +x kind
sudo mv kind /usr/local/bin/
```

 Verify KIND installation:
```bash
kind version
```
**Sample Outpt**

![image](https://github.com/user-attachments/assets/2a781f9d-5c5a-4e9d-a2cf-27bdc277a7bf)

---

## **Step 5: Create a KIND Cluster**
 Create a simple single-node cluster:
```bash
kind create cluster --name my-cluster
```
**Sample Outpt**

![image](https://github.com/user-attachments/assets/a4a5502a-63b3-4604-8c8a-2aab5ba02380)

 Check if the cluster is running:
```bash
kubectl get nodes
```
**Sample Outpt**

![image](https://github.com/user-attachments/assets/a020ee6e-f1de-4acd-ba08-0a2871eabe3c)

---

## **Step 6: Verify Cluster Setup**
 Check cluster status:
```bash
kind get clusters
```
**Sample Outpt**

![image](https://github.com/user-attachments/assets/ccf169b0-5a9a-420f-aec2-3505741f50eb)

 Get Kubernetes cluster info:
```bash
kubectl cluster-info
```
**Sample Outpt**

![image](https://github.com/user-attachments/assets/681e94b9-a3c9-4903-bad0-cdc0fffe23d8)

 Get running pods in all namespaces:
```bash
kubectl get pods -A
```
**Sample Outpt**

![image](https://github.com/user-attachments/assets/738bb519-4b06-4fa8-ab5d-53273b5b489e)

---

## **Step 8: Delete KIND Cluster (Optional)**
If you want to delete the cluster, run:
```bash
kind delete cluster --name my-cluster
```
**Sample Outpt**

![image](https://github.com/user-attachments/assets/4814c894-1dbb-4cb9-83ee-5fc5a52c34f2)

---

## **Summary of Commands**
| **Step** | **Command** |
|----------|------------|
| Update system | `sudo apt update && sudo apt upgrade -y` |
| Install Docker | `sudo apt install -y docker.io` |
| Enable Docker | `sudo systemctl enable --now docker` |
| Check Docker version | `docker --version` |
| Install kubectl | `curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"` |
| Move kubectl to bin | `chmod +x kubectl && sudo mv kubectl /usr/local/bin/` |
| Verify kubectl | `kubectl version --client` |
| Install KIND | `curl -Lo ./kind "https://kind.sigs.k8s.io/dl/latest/kind-linux-amd64"` |
| Move KIND to bin | `chmod +x kind && sudo mv kind /usr/local/bin/` |
| Verify KIND | `kind version` |
| Create KIND cluster | `kind create cluster --name my-cluster` |
| Check nodes | `kubectl get nodes` |
| Check cluster info | `kubectl cluster-info` |
| Delete KIND cluster | `kind delete cluster --name my-cluster` |

---
