## **What is a Kubernetes Service?**
Kubernetes **Service** is a stable network endpoint that allows communication between different parts of an application.

 **Why do we need a Service?**  
- Pods have dynamic (changing) IPs.  
- Services provide a fixed IP and DNS name.  
- Services allow Pods to communicate securely.

### **Types of Kubernetes Services**
1. **ClusterIP** (Default)  
2. **NodePort**  
3. **LoadBalancer**  
4. **ExternalName**

---

## **Types of Kubernetes Services (With Examples)**

### **ClusterIP (Default)**
- **Used for internal communication** within the Kubernetes cluster.
- ClusterIP gives a stable IP for your application within the cluster.
- Using this service we can’t be accessed from outside the cluster.

#### **Example: ClusterIP Service**
```yaml
apiVersion: v1
kind: Service
metadata:
  name: my-clusterip-service
spec:
  selector:
    app: my-app
  ports:
    - protocol: TCP
      port: 80
      targetPort: 8080
  type: ClusterIP
```
 **How to access it?**  
```bash
kubectl get svc
curl http://my-clusterip-service:80
```

---

### **NodePort** (Port Range must be between 30000-32767)
- NodePort is opening a specific port on every node in the cluster.
- Usig nodeport we can access the application externally by using nodeip followed by port number.

#### **Example: NodePort Service**
```yaml
apiVersion: v1
kind: Service
metadata:
  name: my-nodeport-service
spec:
  selector:
    app: my-app
  ports:
    - protocol: TCP
      port: 80
      targetPort: 8080
      nodePort: 30007  # Must be between 30000–32767
  type: NodePort
```
 **How to access it?**  
```bash
kubectl get svc
curl http://<NodeIP>:30007
```

---

### **LoadBalancer**
- LoadBalancer service is used for when you want to make your application accessible from outside the kubernetes cluster.
- LoadBalancer service automatically distribute incoming traffic across multiple pods. Ensuring that no single pod is overloaded. This load distribution 
  enhases the availability and scalability of your application.
- LoadBalancer Used in **cloud environments** (AWS, GCP, Azure).

#### **Example: LoadBalancer Service**
```yaml
apiVersion: v1
kind: Service
metadata:
  name: my-loadbalancer-service
spec:
  selector:
    app: my-app
  ports:
    - protocol: TCP
      port: 80
      targetPort: 8080
  type: LoadBalancer
```
 **How to access it?**  
```bash
kubectl get svc
curl http://<EXTERNAL-IP>:80
```

---

### **ExternalName**
- Maps a Kubernetes Service to an **external domain** (e.g., Google, AWS RDS).
- Used when Kubernetes Pods need to access an **external service**.

#### **Example: ExternalName Service**
```yaml
apiVersion: v1
kind: Service
metadata:
  name: my-external-service
spec:
  type: ExternalName
  externalName: example.com
```
 **How to access it?**  
```bash
kubectl get svc
curl http://my-external-service
```

---

## **Summary Table**
| Service Type  | Use Case | Accessible From |
|--------------|---------|----------------|
| **ClusterIP** | Internal Pod-to-Pod communication | Inside the cluster |
| **NodePort** | Exposes service via `<NodeIP>:<Port>` | Outside the cluster |
| **LoadBalancer** | Uses Cloud Provider's Load Balancer | External world |
| **ExternalName** | Maps to an external domain | Inside the cluster |

---

✅ **ClusterIP** – For internal microservices.  
✅ **NodePort** – For simple external access.  
✅ **LoadBalancer** – Best for cloud-based apps.  
✅ **ExternalName** – When your app needs an external API.  

---
