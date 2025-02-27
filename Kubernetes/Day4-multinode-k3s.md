# **Day4:-**

## **1)How to set up Multi-node kubernetes cluster using k3s?**

Here’s a **detailed step-by-step guide** to setting up a **multi-node Kubernetes cluster using K3s**  

---

## **Step-by-Step: Install a Multi-Node Kubernetes Cluster using K3s**  

### **Prerequisites**
- 1 **Master Node** (Control Plane)  
- 1 or more **Worker Nodes**  
- OS: **Ubuntu 22.04+** (or similar)  
- Each node must have **at least 2GB RAM and 2 vCPUs**  
- User should have **sudo** privileges  
- **Firewalls & Security Groups** should allow:
  - **Port 6443** (for API communication)
  - **Port 8472** (Flannel VXLAN traffic)
  - **Port 10250** (Kubelet metrics)
  - **Port 2379-2380** (etcd communication)

---

## **Step 1: Update All Nodes**
Run this on **all nodes** (Master & Workers):
```bash
sudo apt update && sudo apt upgrade -y
```
---

## **Step 2: Install K3s on the Master Node**
 **Run the K3s Installer on Master**  
```bash
curl -sfL https://get.k3s.io | sh -s - --write-kubeconfig-mode 644
```
 **Check if the cluster is running**  
```bash
sudo kubectl get nodes
```
 **Retrieve the K3s Token** (Save this for Worker Nodes)  
```bash
sudo cat /var/lib/rancher/k3s/server/node-token
```
---

## **🔗 Step 3: Install K3s on Worker Nodes**
Run the following on **each worker node**, replacing `masternode/controlplane<MASTER_IP>` and `<TOKEN>`:
```bash
curl -sfL https://get.k3s.io | K3S_URL="https://172.31.13.49:6443" K3S_TOKEN="K10b944b48298edf1ff07cbd53b58ce15867cfe9d5f49ecb85d26862367d10c1571::server:e6ffa4fd23df815de1d991e7e03efe0b" sh -
```
Verify the worker has joined:
```bash
sudo kubectl get nodes
```
You should see both **master and worker nodes** listed.

---

## **Step 4: Verify the Multi-Node Cluster**
Check cluster status:
```bash
sudo kubectl get nodes -o wide
```
Check pods running:
```bash
sudo kubectl get pods -A
```
---


## **WorkerNode**

On a **Kubernetes worker node**, the `crictl` (Container Runtime Interface CLI) command is used to interact with the container runtime (usually **containerd** or **CRI-O**) for managing pods and containers.  

Here are the **important `crictl` commands** for a **K8s worker node**:

---

### **Basic `crictl` Commands for Worker Nodes**
#### 1. **List all running pods on the worker node**
```bash
crictl pods
```
This shows **Pod ID, Name, Namespace, State, and container count**.

#### 2. **Inspect a specific pod**
```bash
crictl inspectp <POD_ID>
```
Replace `<POD_ID>` with the actual pod ID from `crictl pods`.

#### 3. **List all running containers**
```bash
crictl ps
```
This shows **Container ID, image, state (running/exited), and uptime**.

#### 4. **List all containers (including stopped ones)**
```bash
crictl ps -a
```

#### 5. **Inspect a specific container**
```bash
crictl inspect <CONTAINER_ID>
```
Replace `<CONTAINER_ID>` with the container ID from `crictl ps`.

---

### **Container Management Commands**
#### 6. **Check logs of a running container**
```bash
crictl logs <CONTAINER_ID>
```
Useful for **debugging** application issues.

#### 7. **Stop a running container**
```bash
crictl stop <CONTAINER_ID>
```

#### 8. **Start a stopped container**
```bash
crictl start <CONTAINER_ID>
```

#### 9. **Remove a container**
```bash
crictl rm <CONTAINER_ID>
```

#### 10. **Pull a container image manually**
```bash
crictl pull <IMAGE_NAME>
```
Example:
```bash
crictl pull nginx:latest
```

#### 11. **List all images**
```bash
crictl images
```

#### 12. **Remove an image**
```bash
crictl rmi <IMAGE_NAME>
```

---

### **Troubleshooting & System Info**
#### 13. **Check which container runtime is used**
```bash
crictl info | grep runtimeType
```
Expected output (for **containerd** in K3s):
```json
"runtimeType": "containerd"
```

#### 14. **Check container runtime service status**
```bash
systemctl status containerd
```

#### 15. **Restart the container runtime (containerd)**
```bash
systemctl restart containerd
```

---

### **Debugging Issues on the Worker Node**
If `crictl` commands **fail**, check:
1. **Make sure the container runtime is running**  
   ```bash
   systemctl status containerd
   ```
   If not running, start it:
   ```bash
   systemctl start containerd
   ```

2. **Ensure K3s agent is running (for K3s-based worker nodes)**  
   ```bash
   systemctl status k3s-agent
   ```
   If stopped, restart it:
   ```bash
   systemctl restart k3s-agent
   ```

3. **Check K3s logs for issues**  
   ```bash
   journalctl -u k3s-agent -f
   ```

---

### **How to Use `kubectl` on a Worker Node?**
By default, `kubectl` is usually configured on the **control plane (master node)**. If you want to use `kubectl` on a worker node:
```bash
export KUBECONFIG=/etc/rancher/k3s/k3s.yaml
kubectl get nodes
```
(Ensure the K3s kubeconfig file is correctly set up.)

---

### **Summary**
| Command | Description |
|---------|-------------|
| `crictl pods` | List all running pods |
| `crictl inspectp <POD_ID>` | Get details of a specific pod |
| `crictl ps` | List running containers |
| `crictl ps -a` | List all containers (including stopped) |
| `crictl logs <CONTAINER_ID>` | Show logs of a container |
| `crictl stop <CONTAINER_ID>` | Stop a container |
| `crictl start <CONTAINER_ID>` | Start a stopped container |
| `crictl rm <CONTAINER_ID>` | Remove a container |
| `crictl pull <IMAGE_NAME>` | Pull a container image |
| `crictl images` | List all images |
| `crictl rmi <IMAGE_NAME>` | Remove an image |
| `crictl info | grep runtimeType` | Check the container runtime type |
| `systemctl status containerd` | Check if container runtime is running |
| `systemctl restart containerd` | Restart container runtime |
 
---

## **Step 5: Uninstall K3s (If Needed)**
On the **Master Node**:
```bash
/usr/local/bin/k3s-uninstall.sh
```
On **Worker Nodes**:
```bash
/usr/local/bin/k3s-agent-uninstall.sh
```
---
