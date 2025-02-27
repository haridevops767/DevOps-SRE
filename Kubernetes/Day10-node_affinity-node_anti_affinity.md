### **Node Affinity & Node Anti-Affinity in Kubernetes (Step by Step Explanation)**  

In Kubernetes, **Node Affinity** and **Node Anti-Affinity** help control where a pod is scheduled by defining rules based on node labels.

---

## **1. What is Node Affinity?**
- **Node Affinity** allows you to specify that a pod should be scheduled **on specific nodes** based on labels.  
- It is used when you **want** a pod to run on a particular node or group of nodes.  

### **Types of Node Affinity**
1. **Required (Hard Rule)** (`requiredDuringSchedulingIgnoredDuringExecution`)  
   - If no matching node is found, the pod will not be scheduled.  
2. **Preferred (Soft Rule)** (`preferredDuringSchedulingIgnoredDuringExecution`)  
   - Kubernetes will try to place the pod on the preferred node, but if not available, it will schedule it on any available node.  

---

## **2. Example of Node Affinity**
### **Step 1: Label a Node**
First, add a label to a specific node:
```sh
kubectl label nodes worker-node-1 environment=production
```
This labels `worker-node-1` with `environment=production`.

**Example Output**

![image](https://github.com/user-attachments/assets/9d879a05-fe2d-4d43-8952-3a909d35b0b3)

### **Step 2: Define a Pod with Node Affinity**
Create a file `node-affinity-pod.yaml`:
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-pod
spec:
  affinity:
    nodeAffinity:
      requiredDuringSchedulingIgnoredDuringExecution:
        nodeSelectorTerms:
        - matchExpressions:
          - key: environment
            operator: In
            values:
            - production
  containers:
    - name: nginx
      image: nginx
```
### **Step 3: Apply the Pod**
```sh
kubectl apply -f node-affinity-pod.yaml
```
- This pod will **only** be scheduled on a node labeled `environment=production`.
- If no such node exists, the pod will **remain in the Pending state**.

**Example Output**

![image](https://github.com/user-attachments/assets/a19ae166-0a88-4fb0-a03a-dad65560a902)
![image](https://github.com/user-attachments/assets/acc5b7ba-6ef5-4d77-8f67-b311fcd4fe0f)

---

## **3. What is Node Anti-Affinity?**
- **Node Anti-Affinity** prevents a pod from being scheduled on certain nodes.  
- It is useful when you **want to spread pods across different nodes** for high availability.

### **Example of Node Anti-Affinity**
This ensures that two similar pods **do not run on the same node**.

### **Step 1: Define a Pod with Node Anti-Affinity**
Create a file `node-anti-affinity-pod.yaml`:
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-pod
spec:
  affinity:
    podAntiAffinity:
      requiredDuringSchedulingIgnoredDuringExecution:
        labelSelector:
          matchLabels:
            app: my-app
        topologyKey: "kubernetes.io/hostname"
  containers:
    - name: nginx
      image: nginx
```
### **Step 2: Apply the Pod**
```sh
kubectl apply -f node-anti-affinity-pod.yaml
```
- This ensures that **multiple pods with the same label (`app: my-app`) do not run on the same node**.
- The `topologyKey: "kubernetes.io/hostname"` means the pods will be scheduled on different nodes.

---

## **4. Key Differences Between Node Affinity & Node Anti-Affinity**
| Feature            | Node Affinity | Node Anti-Affinity |
|--------------------|--------------|--------------------|
| Purpose           | Schedule pods on specific nodes | Prevent pods from running on the same node |
| Example Use Case  | Run a database on high-memory nodes | Spread application pods across different nodes |
| Configuration     | `nodeAffinity` | `podAntiAffinity` |

---

## **5. Verify the Pod Placement**
Run:
```sh
kubectl get pods -o wide
```
This shows which nodes the pods are running on.

---

This is a clear step-by-step explanation of **Node Affinity and Node Anti-Affinity** in Kubernetes. Let me know if you need more details.
