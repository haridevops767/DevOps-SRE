### **Step-by-Step Guide to Learning Helm with K3s in an EC2 Spot Instance**  

Helm is a package manager for Kubernetes that helps you define, install, and upgrade applications. It uses "charts" to deploy applications efficiently. Below is a structured way to learn and practice Helm using K3s on an AWS EC2 Spot instance.  

---

## **Step 1: Understand Helm Basics**  
Before diving into practice, you should understand the key Helm concepts:  

### **1.1 What is Helm?**  
- Helm is a package manager for Kubernetes, similar to APT for Ubuntu or YUM for CentOS.  
- It simplifies application deployment and management in Kubernetes.  

### **1.2 Key Helm Concepts:**  
1. **Chart:** A Helm package that contains Kubernetes resources (YAML files).  
2. **Release:** A deployed instance of a Helm chart.  
3. **Repository:** A place where Helm charts are stored.  
4. **Values:** Configuration settings for a Helm chart.  
5. **Templates:** YAML files with dynamic placeholders that Helm fills in during deployment.  

---

## **Step 2: Set Up an AWS EC2 Spot Instance for K3s and Helm**  

### **2.1 Launch an EC2 Spot Instance**  
1. Go to AWS Management Console → EC2 → Spot Requests.  
2. Choose **Ubuntu 22.04** as the base OS.  
3. Select an instance type like **t2.medium** or higher.  
4. Configure storage (10GB is sufficient for basic Helm practice).  
5. Open required ports (22 for SSH, 6443 for K3s API, 80/443 if needed).  
6. Launch the instance and SSH into it:  
   ```bash
   ssh -i your-key.pem ubuntu@<ec2-public-ip>
   ```

### **2.2 Install K3s (Lightweight Kubernetes)**  
1. Run the K3s installation command:  
   ```bash
   curl -sfL https://get.k3s.io | sh -
   ```
2. Check K3s status:  
   ```bash
   systemctl status k3s
   ```
3. Verify cluster is running:  
   ```bash
   kubectl get nodes
   ```
4. If kubectl is not found, add it to the path:
   ```bash
   export KUBECONFIG=/etc/rancher/k3s/k3s.yaml
   ```

---

## **Step 3: Install and Configure Helm**  

### **3.1 Install Helm on the EC2 Instance**  
1. Download and install Helm:  
   ```bash
   curl -fsSL https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3 | bash
   ```

**Sample Output**

![image](https://github.com/user-attachments/assets/a28f841c-c57f-4e5b-b02f-f9635299aa6e)
   
3. Verify installation:  
   ```bash
   helm version
   ```
**Sample Output**

![image](https://github.com/user-attachments/assets/b0bcf97e-04a5-44e1-9e7a-83e7335bb2fa)

### **3.2 Add Helm Repositories**  
1. Add the official Helm chart repository:  
   ```bash
   helm repo add stable https://charts.helm.sh/stable
   ```
**Sample Output**

![image](https://github.com/user-attachments/assets/f1967971-b17c-489a-8049-6d48cf8f9726)

2. Update repositories:  
   ```bash
   helm repo update
   ```
**Sample Output**

![image](https://github.com/user-attachments/assets/8e018c2a-79fb-4317-a8a8-f1b23e289c69)

---

## **Step 4: Deploy Applications Using Helm**  

### **4.1 Install an Example Application (NGINX)**  
1. Search for NGINX chart:  
   ```bash
   helm search repo nginx
   ```
   **Sample Output**

    ![image](https://github.com/user-attachments/assets/dbcf71a9-0066-4acb-add8-0ca4ba3344be)

2. Install NGINX using Helm:  
   ```bash
   helm install my-nginx stable/nginx-ingress
   ```
   **Sample Output**

    ![image](https://github.com/user-attachments/assets/98171ceb-9995-4b89-8c39-498471536f6b)
  
3. Verify installation:  
   ```bash
   helm list
   ```
   **Sample Output**

   ![image](https://github.com/user-attachments/assets/cb0ab70b-dd60-4685-a315-fbd32de6540e)

4. Check the running pods:  
   ```bash
   kubectl get pods -A
   ```
   **Sample Output**

   ![image](https://github.com/user-attachments/assets/31045b64-8596-499f-a250-e845743390cd)

---

### **Helm Repositories**  
Helm uses repositories to store and distribute charts.
```bash
helm repo add bitnami https://charts.bitnami.com/bitnami
helm repo update
```
**Sample Output**

![image](https://github.com/user-attachments/assets/117be836-55ce-4856-9fd4-901e272864a0)

### **Installing an Application Using Helm**  
Example: Install **NGINX** using Helm.
```bash
helm install my-nginx bitnami/nginx
```
**Sample Output**

![image](https://github.com/user-attachments/assets/a1830ca3-9eab-47f9-96aa-fbee419cdcd9)

Check the deployed application:
```bash
kubectl get pods
```
**Sample Output**

![image](https://github.com/user-attachments/assets/7b3d36bc-b0e2-4dd8-8e96-0b15cc8822ac)

check theservice:
```bash
kubectl get svc
```
**Sample Output**

![image](https://github.com/user-attachments/assets/ac08a964-d103-4b05-aab8-3fdb1fbea871)





 **Find the Assigned Port:**
   ```bash
   kubectl get svc my-nginx
   ```
   **Sample Output**

   ![image](https://github.com/user-attachments/assets/05b2eecb-1a2f-46ef-aee7-a742282b44d7)

   Here, **30784** is the exposed port.

3. **Access NGINX**
   - Get your **EC2 Public IP** from the AWS Console.
   -   - Open in browser:
     ```
     http://65.2.180.17:30784/
     ```
     **Sample Output**

      ![Screenshot 2025-02-05 171325](https://github.com/user-attachments/assets/dd2451bc-c72e-404f-9e0b-fab129fe0df2)

   - You should see the default NGINX welcome page. 🎉

---

