# **Day2:-**

# 1. Introduction to Kubernetes

**1)What is kubernetes?**

  Kubernetes is an open-source container orchestration platform used for automating deployment,    scaling, and management of containerized applications.

**2)Why kubernetes?**

  Kubernetes is needed because it helps manage containerized applications easily. Without Kubernetes, handling multiple containers manually is complex and time-consuming.
  
  Simple Example:-
Imagine we are using an online shopping website running in containers. If traffic increases Kubernetes automatically adds more containers to handle the load. When traffic decreases, it removes extra containers to save resources.
That’s why Kubernetes is powerful and widely used.

**3)Explain kubernetes Architecture?**

  ### **Kubernetes Architecture**  

Kubernetes follows a **master-worker architecture** where the **Master Node** manages the cluster, and **Worker Nodes** run the applications.  

---

### **Main Components of Kubernetes Architecture**  

### **i) Master Node/Control Plane**  
The **Master Node/Control Plane** controls and manages the cluster. 

It has 4 main components:  

- **API Server** → It's a central component of k8s, which act as a bridge between the user and the cluster. All components of cluster will communicate with the API Server to perform their actions.
- **etcd (Cluster Database)** → etcd Stores all Kubernetes data (like cluster state & configurations).    
- **Scheduler** → Scheduler is component of kubernetes, which is responsible for assigning the pods to worker nodes and to maintain the desired state of the cluster.  
- **Controller Manager** →  Controller Manager constantly monitors the cluster and makes sure the desired state of the cluster is maintained. (e.g., restarts failed pods).

---

### **ii) Worker Nodes (Where Apps Run)**  
Worker Nodes **run the actual applications** inside containers. Each Worker Node has:  

- **Kubelet** → Communicates with the master and ensures containers are running properly.  
- **Kube Proxy** → It assigns dynamic IP to pods. It runs on each node to make sure that each pod will get unique IP.  
- **Container Runtime (Docker, containerd, CRI-O, etc.)** → Container Runtime responsible for running containers within pods.  

--- 

### **Pod, Node, and Cluster Concepts**
- **Pod**: The smallest unit in Kubernetes. A pod can contain one or more containers that share resources like storage and network.
- **Node**: A physical or virtual machine in the Kubernetes cluster that runs the pods.
- **Cluster**: A set of nodes (master and worker) that run your applications and manage them using Kubernetes.

---

## **Kubernetes Objects**

Kubernetes uses several **objects** to manage applications and resources in the cluster. Here are some of the key objects:

- **Pod**: The smallest unit in Kubernetes. A **pod** can have one or more containers that run your application and share resources like storage and networking.

- **ReplicaSet**: A ReplicaSet is a kubernetes resource that helps maintain a desired number of identical pods. It ensures that the specified number of replica pods are always running and it automatically creates or deletes pods to match the specified number.

- **Deployment**: A Deployment in Kubernetes is used to manage and update applications running in Pods. It ensures that the correct number of replicas (copies) of your application are running and can update them without downtime.

- **Service**: Exposes your application to the outside world or allows communication between pods inside the cluster. It creates a stable IP address for the pods.

- **Ingress**: Manages **external access** to your applications, typically over HTTP/HTTPS. It helps route traffic to different services based on URL paths or domain names.

- **ConfigMap**: Stores **non-sensitive configuration data** like environment variables, configuration files, or command-line arguments that can be used by pods.

- **Secret**: Stores **sensitive information** like passwords, API keys, and tokens securely. It ensures that this data is encrypted and not exposed in plain text.

- **Namespace**: A **Namespace** in Kubernetes is like a **separate workspace** inside a cluster. It helps organize and manage different resources **without mixing them up**.  

For example:  
- `dev` namespace → for development  
- `test` namespace → for testing  
- `prod` namespace → for production  

This makes it easier to manage large applications and teams.

---

## **Working with Pods**  

### **Creating a Pod using `kubectl run`**  
By using below command we can create pod:  

```sh
kubectl run tomcat --image=tomcat --port=8080
```

**Example Output:-**

![Screenshot 2025-01-29 162157](https://github.com/user-attachments/assets/7961aec2-99aa-4376-be9a-b87ebd407c48)


**Check the Pod status:** 

By using below command we can see the pods of default namespace.

```sh
kubectl get pods
```

**Example Output:-**

![Screenshot 2025-01-29 162224](https://github.com/user-attachments/assets/1928b209-f50a-4e02-957c-56c64677a3d0)


**Describe the Pod for more details:**  

By using below command we can see the all details of the pod.

```sh
kubectl describe pod tomcat
```
**Example Output:-**

 ![Screenshot 2025-01-29 170324](https://github.com/user-attachments/assets/d21bd35b-c679-4361-b009-c6308855d613)


**Delete the Pod:**

By using below command we can delete the pod.

```sh
kubectl delete pod tomcat
```

**Example Output:-**

![image](https://github.com/user-attachments/assets/643acb41-c367-48a4-93a8-e1bb0d43d269)

---
