## **How to install Minikube?**

## **Install Minikube on Ubuntu 22.04 Server – Step-by-Step Guide**

### **Prerequisites**
Before installing Minikube, ensure your system meets the following requirements:

1. **Ubuntu 22.04 Server**
2. **64-bit CPU (x86_64 or amd64)**
3. **Minimum System Requirements:**
   - **2 CPUs**
   - **2GB RAM (4GB recommended)**
   - **20GB free disk space**
4. **A Container or Virtualization Environment** (Docker, containerd, or KVM)
5. **User with sudo privileges**

---

## **Step 1: Update the System**
Before installing Minikube, update the package list and upgrade existing packages:
```bash
sudo apt update && sudo apt upgrade -y
```

---

## **Step 2: Install Required Dependencies**
Minikube requires some dependencies like `curl`, `conntrack`, and a container runtime (Docker).

```bash
sudo apt install -y curl wget apt-transport-https conntrack
```

---

## **Step 3: Install a Container Runtime (Docker)**
Minikube uses a container runtime like **Docker**, **containerd**, or **CRI-O**. Here, we install Docker:

```bash
sudo apt install -y docker.io
```

Check Docker status:
```bash
sudo systemctl enable --now docker
docker --version
```
**Sample Outpt**

![image](https://github.com/user-attachments/assets/c5259bab-4e08-41ed-b9b4-c8bf749415d7)

---

## **Step 4: Download and Install Minikube**
Run the following command to install Minikube:
```bash
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
```
**Sample Outpt**

![image](https://github.com/user-attachments/assets/030b65fa-ebb2-4e3b-98fb-9f519600ff47)

Move the binary to `/usr/local/bin/` for system-wide access:
```bash
sudo install minikube-linux-amd64 /usr/local/bin/minikube
```
Verify installation:
```bash
minikube version
```
**Sample Outpt**

![image](https://github.com/user-attachments/assets/bd188e65-bf4e-4b14-8dc7-ef3fba3563ed)

---

## **Step 5: Install kubectl (Kubernetes CLI)**
Minikube requires `kubectl` to interact with the Kubernetes cluster.

 Download and install `kubectl`:
```bash
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
```
**Sample Outpt**

![image](https://github.com/user-attachments/assets/5dd12eb1-e2c3-4367-8c7f-3220b71df897)


 Make it executable:
```bash
chmod +x kubectl
sudo mv kubectl /usr/local/bin/
```

 Verify `kubectl` installation:
```bash
kubectl version --client
```
**Sample Outpt**

![image](https://github.com/user-attachments/assets/30cbd0be-973e-471d-a94c-ddb645b2be5e)

---

## **Step 6: Start Minikube**
Now, start Minikube using the `docker` driver:
```bash
minikube start --driver=docker --force
```
**Sample Outpt**

![image](https://github.com/user-attachments/assets/b945553c-7f99-459d-b0d9-9f2afa024a7d)

- If you are using a **VM** (KVM or VirtualBox), use:
  ```bash
  minikube start --driver=kvm2
  ```
- If you are using **containerd**, use:
  ```bash
  minikube start --container-runtime=containerd
  ```

Check if Minikube started successfully:
```bash
kubectl get nodes
```
**Sample Outpt**

![image](https://github.com/user-attachments/assets/0cab29f3-40f3-4db7-accb-afbcc836be2b)

---

## **Step 7: Verify Minikube Cluster**
### Check Cluster Status:
```bash
minikube status
```
**Sample Outpt**

![image](https://github.com/user-attachments/assets/a6ec7617-deae-4621-8384-2fbcf4259b16)

### Get Cluster Info:
```bash
kubectl cluster-info
```
**Sample Outpt**

![image](https://github.com/user-attachments/assets/5950af00-8892-4ed0-8655-ec7d1212b929)

### List Running Pods:
```bash
kubectl get pods -A
```
**Sample Outpt**

![image](https://github.com/user-attachments/assets/cac101f4-c798-44ed-b163-f23a6e1128c9)

---

## **Step 8: Stop and Delete Minikube (Optional)**
If you want to stop or delete Minikube:

- **Stop Minikube**
  ```bash
  minikube stop
  ```
- **Delete Minikube Cluster**
  ```bash
  minikube delete
  ```

---

## **Summary of Commands**
| **Step** | **Command** |
|----------|------------|
| Update system | `sudo apt update && sudo apt upgrade -y` |
| Install dependencies | `sudo apt install -y curl wget apt-transport-https conntrack` |
| Install Docker | `sudo apt install -y docker.io` |
| Download Minikube | `curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64` |
| Install Minikube | `sudo install minikube-linux-amd64 /usr/local/bin/minikube` |
| Verify Minikube | `minikube version` |
| Install kubectl | `curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"` |
| Move kubectl to bin | `chmod +x kubectl && sudo mv kubectl /usr/local/bin/` |
| Verify kubectl | `kubectl version --client` |
| Start Minikube | `minikube start --driver=docker` |
| Check nodes | `kubectl get nodes` |
| Check cluster info | `kubectl cluster-info` |
| Enable dashboard | `minikube addons enable dashboard` |
| Start dashboard | `minikube dashboard` |

---
