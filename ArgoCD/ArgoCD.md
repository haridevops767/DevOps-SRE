# What is Argo CD?
  ArogoCD is a tool for deploying applications on kubernetes, ArgoCD can pull updated code from git repositories and deploy it to directly kubernetes.

---

## **Step-by-Step Guide to Argo CD with Hands-on**  

### **1. Prerequisites**  
Before starting with Argo CD, ensure you have the following:  
- **Kubernetes Cluster** (Minikube, K3s, EKS, AKS, GKE, or any other Kubernetes cluster)  
- **kubectl** installed on your local machine  
- **GitHub repository** to store application manifests (YAML files)  

---
#### **Install K3s on the Ubuntu Spot Instance**
1. Update the system Packages:
   ```bash
   sudo apt update && sudo apt upgrade -y
   ```

2. Install Prerequisites `curl` and `wget` :
   ```bash
   sudo apt install curl wget -y
   ```

3. Install K3s (lightweight Kubernetes):
   ```bash
   curl -sfL https://get.k3s.io | sh -
   ```
   

4. Verify K3s installation:
   ```bash
   kubectl version
   kubectl get nodes
   ```
   You should see the node in a `Ready` state.

   **Sample Output**

   ![image](https://github.com/user-attachments/assets/337dce9c-574f-4010-99a2-e2e6ddb562b3)

---

### **2. Install Argo CD**  
Argo CD runs as a set of Kubernetes resources inside a cluster.

#### **Option 1: Install using kubectl (Official Method)**  
Run the following commands to install Argo CD in the `argocd` namespace:  

```sh
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```
**Sample Output**

![image](https://github.com/user-attachments/assets/15f38ac3-4e01-4f34-8679-2315cc1a11d8)
![image](https://github.com/user-attachments/assets/3d41e7c5-3c7e-4bdc-bb46-97f77f138ff3)

Verify the pods are running:  
```sh
kubectl get pods -n argocd
```
**Sample Output**

![image](https://github.com/user-attachments/assets/7d3eb1db-a114-4ec4-a767-78f9a406df71)

---

### **3. Access the Argo CD API Server**  
By default, the Argo CD API server is not exposed externally. To access it:

#### **Edit the argocd-server service to use NodePort:**
Run:
```sh
kubectl edit svc argocd-server -n argocd
```
Update the type field from ClusterIP to NodePort. Save and exit the editor.

**Sample Output**

![image](https://github.com/user-attachments/assets/89d1d3a8-9b05-439d-bc97-87a01a11a57c)

Run: 
```sh
kubectl get svc argocd-server -n argocd
```
**Sample Output**

![image](https://github.com/user-attachments/assets/27f1b166-1a9a-47be-b44c-d947d30ac443)

---

### **4. Login to Argo CD**  
To log in, get the initial admin password:  

```sh
kubectl get secret -n argocd argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 --decode
```
**Sample Output**

![image](https://github.com/user-attachments/assets/8914006e-d4b3-48c1-9590-1dae37cac0db)

password: gPQ0SMrYDBsDNyb-

**Login to Argo CD: Use the following command to log in to the Argo CD server:**

```sh
argocd login 35.154.152.106:30287 --username admin --password gPQ0SMrYDBsDNyb-
```
**Sample Output**

![image](https://github.com/user-attachments/assets/5c85b3ce-61c8-4fad-a8a7-3a8f699f61f2)

**Open your browser and navigate to the following URL:**

http://<NODE_IP>:<NODEPORT>
Replace <NODE_IP> with your server’s IP address and <NODEPORT> with the NodePort
```sh
35.154.152.106:30287
```
**Sample Output**

![image](https://github.com/user-attachments/assets/87e9be2f-4586-4661-b0dd-62bab9cfb00b)

If using the UI, enter `admin` as the username and the password is gPQ0SMrYDBsDNyb- retrieved above.

![image](https://github.com/user-attachments/assets/11dfe758-63a3-4f9e-b95a-e64174dcffa3)

![image](https://github.com/user-attachments/assets/fbe3e958-20e3-42bb-8042-46031fead116)

---

### **5. Deploy a Sample Application using Argo CD**  

Argo CD manages applications defined in Git repositories. Here’s how to deploy an app:

#### **Step 1: Create a Git Repository**
- Create a repository on GitHub (e.g., `argocd-sample-app`)  
- Add a Kubernetes manifest inside it (`deployment.yaml`, `service.yaml`)  

Example `deployment.yaml`:
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  labels:
    app: nginx
spec:
  replicas: 1
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
        image: nginx:latest
        ports:
        - containerPort: 80
```

Example `service.yaml`:
```yaml
apiVersion: v1
kind: Service
metadata:
  name: nginx-service
spec:
  selector:
    app: nginx
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
  type: NodePort
```

Push these files to your GitHub repository.

---

#### **Step 2: Create an Application in Argo CD**
Run the following command to create an Argo CD application:

```sh
argocd app create nginx-app \
    --repo https://github.com/AjayRatakonda/argocd-sample-app.git \
    --path . \
    --dest-server https://kubernetes.default.svc \
    --dest-namespace default
```
**Sample Output**

![image](https://github.com/user-attachments/assets/8505a925-a73f-4b40-862a-883b2cf9bac7)

Verify the application is created:
```sh
argocd app list
```
**Sample Output**

![image](https://github.com/user-attachments/assets/d10e5a40-543b-46f1-86a1-39eddec2762c)
![image](https://github.com/user-attachments/assets/f3ff55b9-fa03-4085-98be-cc6bda724e69)

---

#### **Step 3: Sync the Application**
```sh
argocd app sync nginx-app
```

**Sample Output**

![image](https://github.com/user-attachments/assets/0c031796-da5f-4fd9-83ac-d74b6ca9129e)

![image](https://github.com/user-attachments/assets/77e32f2e-a7e9-4e08-a430-575648e5df76)

![image](https://github.com/user-attachments/assets/fb49bd61-267b-48ba-a505-c0f19d65b198)

![image](https://github.com/user-attachments/assets/6edc0e93-237b-423b-8b76-d6c3452e1a50)

This will deploy the application defined in Git.

Check deployment status:
```sh
argocd app get nginx-app
```
**Sample Output**

![image](https://github.com/user-attachments/assets/f8b5cb1a-7531-4dc7-b760-6930587639ac)


---

### **6. Verify Deployment**
Run:
```sh
kubectl get pods
kubectl get svc
```
**Sample Output**

![image](https://github.com/user-attachments/assets/71fb63e2-a2ea-4609-9b3a-dd44daa06356)


To access the application, get the external IP:
```sh
kubectl get svc nginx-service
```
**Sample Output**

![image](https://github.com/user-attachments/assets/a9ad9738-b683-4a41-927e-5237690dc7e2)

Visit the IP in a browser to see the running application.

![image](https://github.com/user-attachments/assets/d399ec42-f209-4ff1-8519-94e7a58e37a0)

---

### **7. Automatic Sync and Self-healing**
Argo CD ensures the state in Git matches the cluster state. If someone changes a resource manually, Argo CD will detect the drift and restore it.

Enable auto-sync:
```sh
argocd app set nginx-app --sync-policy automated
```
**Sample Output**

![image](https://github.com/user-attachments/assets/55d44e01-5a77-445b-ac26-299368505c04)

Now, whenever you push changes to Git, Argo CD will apply them automatically.

# I changed deployment file replica count 1 to 2 and push changes to git hub repo, when changes occur in git repo automatically new pod will be created like below screenshot.

![image](https://github.com/user-attachments/assets/6174534e-ec89-464f-b15d-4343cf40e1ff)

---

### **8. Clean Up**
To delete the application:
```sh
argocd app delete nginx-app
```

To remove Argo CD:
```sh
kubectl delete namespace argocd
```

---

### **Summary**
- Installed Argo CD in a Kubernetes cluster  
- Logged into Argo CD via CLI and UI  
- Created a Git repository with application manifests  
- Deployed and synced an application using Argo CD  
- Enabled auto-sync for self-healing and GitOps  
---
