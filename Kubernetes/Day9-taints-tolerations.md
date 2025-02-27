### **What is Taints & Tolerations in Kubernetes?**  

Taints and tolerations **control which pods can run on which nodes** in a Kubernetes cluster.  

- **Taints (applied to nodes)**: Prevent unwanted pods from running on specific nodes.  
- **Tolerations (applied to pods)**: Allow specific pods to bypass taints and run on those nodes.  

Taints & Tolerations
---------------------
 Taints & Tolerations control which pod can create on which nodes in k8s cluster.

 taint-taint is used to schedule  particular pods to particular node.


There are three conditions in taints:-

1)NoSchedule= for example I have two worker nodes and one pod is running on node1 now I taint node1 with noschedule condition existing pod still running on node1 but when I try to create pods, new pods not created in node1 if we want to create new pods in taint node1 we need to mention toleration key=value:NoSchedule, now pod will be created in node1

2)NoExecute= for example i have one pod running in node1 now i taint node1 with noexecute condition the existing pod will be evicted to anothernode if we want to create a pod in node1 we need to mention tolerations key=value:NoExecute now pod will be created in node1

3)PreferNoSchedule= for example i have 2 worker nodes now i taint node1 with condition PreferNoSchedule now when i try to create pod first it will try to create worker node2 if node2 is tainted with other conditions it will be created in node1 only.

---

### **How Taints & Tolerations Work?**  

1. A node is **tainted** to reject certain pods.  
2. Only pods with a **matching toleration** can be scheduled on that node.  
3. Other pods will **avoid** that node.  

---

### **Example 1: Taint a Node**  
Tainting a node to accept only specific workloads:  
```sh
kubectl taint nodes worker-node-1 key=value:NoSchedule
```
- This means **no pods** will be scheduled on `worker-node-1` **unless they have a matching toleration**.  

**Sample Output**

![image](https://github.com/user-attachments/assets/da6a940d-9c69-4f81-94b5-9dd7c7bb63e0)

I have one master/controlplane and one workernode, now i tainting controlplane node also

```sh
kubectl taint node ip-172-31-45-202 key=value:NoSchedule
```

**Sample Output**

![image](https://github.com/user-attachments/assets/028fd63c-aeb0-42d1-8e1b-277fcefa8cc0)

now i try to create pod, it will not created because all nodes are tainted, see below screenshot pod status is pending, because there is no available node to schedule it on.

```sh
kubectl run nginx --image=nginx:latest --port=80
```

![image](https://github.com/user-attachments/assets/63c64d54-67fe-4f8a-b687-861bd0b3abc8)

---

### **Example 2: Add a Toleration to a Pod**  
A pod can **tolerate** the taint by adding this to its YAML file:  
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-pod
spec:
  tolerations:
  - key: "key"
    operator: "Equal"
    value: "value"
    effect: "NoSchedule"
  containers:
  - name: my-container
    image: nginx
```

Now, this pod can be **scheduled on the tainted node** (`worker-node-1`).

```sh
kubectl create -f pod.yml
```
**Sample Output**

![image](https://github.com/user-attachments/assets/b18c47d9-65fc-4213-b3d8-c576eed0e48b)

**Sample Output**

![image](https://github.com/user-attachments/assets/efd0fd2b-a88c-4f15-85ba-22c1dd834f3e)

---

### **Effects of Taints in Kubernetes**  

| Taint Effect | Behavior |
|-------------|----------|
| **NoSchedule** | No pods can be scheduled unless they have a matching toleration. |
| **PreferNoSchedule** | Tries to avoid scheduling pods, but may still allow it. |
| **NoExecute** | Immediately evicts running pods unless they have a matching toleration. |

---

### **Example 3: Removing a Taint**  
To remove a taint from a node:  
```sh
kubectl taint nodes worker-node-1 key=value:NoSchedule-
```
**Sample Output**

![image](https://github.com/user-attachments/assets/8e418712-0db5-4bb4-8490-5869c5f73887)

now, I try to create pod it wil be created in worker-node-1, because worker-node-1 is available, see below screenshot for your reference

![image](https://github.com/user-attachments/assets/44bb9667-c673-494a-aac4-991c18f434c3)

---

### **Use Cases of Taints & Tolerations**  
- **Dedicated Nodes**: Ensure critical workloads run on specific nodes.  
- **Isolating Applications**: Prevent certain applications from running on general worker nodes.  
- **Handling Special Hardware**: Assign GPU-intensive workloads to GPU-enabled nodes only.

---

### **Real-World Scenarios of Taints and Tolerations in Kubernetes**  

Taints and tolerations are useful in **real-world Kubernetes deployments** to control pod scheduling. Below are some practical scenarios with examples.  

---

### **Scenario 1: Running Critical Applications on Dedicated Nodes**  
**Use Case:**  
- You have a **high-priority database** (like PostgreSQL) that needs to run on a **dedicated node** with SSD storage.  
- You don't want other general workloads to be scheduled on that node.  

**Solution:**  
- **Taint the node** to allow only database-related workloads.  
- **Add a toleration to the database pod** so it can run on that node.  

**Step 1: Taint the Node**  
```sh
kubectl taint nodes db-node dedicated=db:NoSchedule
```
- This prevents **any pods** from running on `db-node` unless they have a matching toleration.  

**Step 2: Add Toleration in Pod YAML**  
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: postgres-db
spec:
  tolerations:
  - key: "dedicated"
    operator: "Equal"
    value: "db"
    effect: "NoSchedule"
  containers:
  - name: postgres-container
    image: postgres
```
- Now, only the `postgres-db` pod can run on `db-node`.  
- Other pods **will not be scheduled** on this node.  

---

### **Key Takeaways**  
- Use **NoSchedule** to prevent pods from being scheduled on specific nodes.  
- Use **NoExecute** to **evict** pods that should not run on a node.  
- Use **PreferNoSchedule** to try to avoid scheduling pods but allow it if needed.  

---
