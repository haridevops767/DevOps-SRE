### **Deploying a StatefulSet with Persistent Storage (PV, PVC) for MySQL on an Ubuntu EC2 Instance**  
---
### **What is a StatefulSet?**  
A **StatefulSet** is a Kubernetes resource used to manage **stateful applications**, like **databases (MySQL, PostgreSQL, MongoDB, etc.)** or applications that need **persistent storage** and **stable identities** across restarts.

### **Why StatefulSet?**  
When running a database in Kubernetes, each instance (pod) needs:  
1. **Stable and Unique Identity** – Each pod gets a **predictable name** (e.g., `db-0`, `db-1`, `db-2`) instead of a random one like Deployments.  
2. **Persistent Storage** – Even if a pod restarts, its data remains intact.  
3. **Ordered Scaling and Rolling Updates** – Pods are created or deleted **one at a time**, maintaining order.  

### **How Does It Work?**  
- If you scale up, new pods are added **sequentially** (`db-0` → `db-1` → `db-2`).  
- If a pod fails, Kubernetes replaces it with the **same identity** and **same storage** (via PersistentVolume).  

### **Example Use Case**  
A **MongoDB ReplicaSet** needs stable pod names (`mongo-0`, `mongo-1`, `mongo-2`) so they can **communicate** reliably. A StatefulSet ensures this stability.  
            
**deploy a MySQL database using StatefulSet in Kubernetes on an Ubuntu EC2 instance** with **Persistent Volume (PV) and Persistent Volume Claim (PVC)**.  

---

## **Prerequisites**
1. **Ubuntu 20.04/22.04 EC2 instance** (t2.medium or higher recommended)
2. **Kubernetes Cluster** (Installed using `kubeadm` or `k3s`, or use Kind/minikube)
3. **kubectl installed**
5. **Storage Provisioner** (For Dynamic PV)

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

## **Step 2: Create Persistent Storage (PV & PVC)**
Kubernetes requires **Persistent Volumes (PV)** to store data for stateful applications.  

### **2.1 Create Persistent Volume (PV)**
Create a YAML file **pv.yaml**:
```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: mysql-pv
spec:
  capacity:
    storage: 5Gi
  accessModes:
    - ReadWriteOnce
  persistentVolumeReclaimPolicy: Retain
  storageClassName: manual
  hostPath:
    path: "/mnt/data"  # Local directory for PV storage
```
Apply the PV:
```bash
kubectl apply -f pv.yaml
```

**Sample Output**

![image](https://github.com/user-attachments/assets/11a58b66-6a17-497b-94d6-5ccfda9eb942)


### **2.2 Create Persistent Volume Claim (PVC)**
Create a YAML file **pvc.yaml**:
```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: mysql-pvc
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 5Gi
  storageClassName: manual
```
Apply the PVC:
```bash
kubectl apply -f pvc.yaml
```

**Sample Output**

![image](https://github.com/user-attachments/assets/643b6975-af46-4127-a3b6-51b2d8295684)

---

## **Step 3: Deploy MySQL StatefulSet**
We can store the MySQL root password in a **Kubernetes Secret** instead of using plain text in the StatefulSet YAML. Here's how you can do it:  

---

### **Create a Secret for MySQL Password**
Run the following command to create the secret:  
```sh
kubectl create secret generic mysql-secret --from-literal=MYSQL_ROOT_PASSWORD=mypassword
```

**Sample Output**

![image](https://github.com/user-attachments/assets/e62f5f9c-627a-470c-9d9a-b15e32758f4a)

Alternatively, we can define the secret in a YAML file (`mysql-secret.yaml`):

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: mysql-secret
type: Opaque
data:
  MYSQL_ROOT_PASSWORD: bXlwYXNzd29yZA==  # Base64 encoded value of "mypassword"
```
Apply the secret:  
```sh
kubectl apply -f mysql-secret.yaml
```
---

### **Create a YAML file mysql-statefulset.yaml**
Update your `StatefulSet` YAML to refer to the secret:

```yaml
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: mysql
spec:
  serviceName: "mysql"
  replicas: 1
  selector:
    matchLabels:
      app: mysql
  template:
    metadata:
      labels:
        app: mysql
    spec:
      containers:
        - name: mysql
          image: mysql:8
          ports:
            - containerPort: 3306
          env:
            - name: MYSQL_ROOT_PASSWORD
              valueFrom:
                secretKeyRef:
                  name: mysql-secret
                  key: MYSQL_ROOT_PASSWORD
            - name: MYSQL_DATABASE
              value: "mydb"
          volumeMounts:
            - name: mysql-pvc
              mountPath: /var/lib/mysql
  volumeClaimTemplates:
    - metadata:
        name: mysql-pvc
      spec:
        accessModes: ["ReadWriteOnce"]
        resources:
          requests:
            storage: 5Gi
```

---

### **Step 3.1: Apply the Updated StatefulSet**
```sh
kubectl apply -f mysql-statefulset.yaml
```

**Sample Output**

![image](https://github.com/user-attachments/assets/9e24b729-c284-4886-b9bf-b0f22a7e58a3)

Now, MySQL will use the password stored in Kubernetes Secrets instead of a plain-text password in the YAML file. 

---

## **Step 4: Create a Service for MySQL**
Create a YAML file **mysql-service.yaml**:
```yaml
apiVersion: v1
kind: Service
metadata:
  name: mysql
spec:
  ports:
    - port: 3306
  selector:
    app: mysql
  clusterIP: None
```
Apply the service:
```bash
kubectl apply -f mysql-service.yaml
```

**Sample Output**

![image](https://github.com/user-attachments/assets/d01e8c7b-20e3-46b6-bcff-a11108b064ec)

---

## **Step 5: Verify the Deployment**
Check the **Pods**:
```bash
kubectl get pods
```

**Sample Output**

![image](https://github.com/user-attachments/assets/3acc5078-880b-4a95-9d49-6b34e55d5e7b)

Check the **Persistent Volume & Claim**:
```bash
kubectl get pv,pvc
```

**Sample Output**

![image](https://github.com/user-attachments/assets/06d8754a-e0d3-4578-826d-d295b6829429)


Check the **StatefulSet**:
```bash
kubectl get statefulset
```

**Sample Output**

![image](https://github.com/user-attachments/assets/b482ca1c-76c9-4d4c-9b7a-73b80cb9616a)

---

## **Step 6: Access MySQL Database**
Once MySQL is running, connect to it:
```bash
kubectl exec -it mysql-0 -- mysql -u root -p
```
**Sample Output**

![image](https://github.com/user-attachments/assets/166d8d55-4906-411f-86f4-0b19241a887e)

Enter the password (`mypassword`) and run:
```sql
SHOW DATABASES;
```

**Sample Output**

![image](https://github.com/user-attachments/assets/469032d6-a19e-4b12-aaa1-6d658234fe0b)

---

## **Step 7: Test Data Persistence**
1. Create a table and insert data:
   ```sql
   USE mydb;
   CREATE TABLE users (id INT PRIMARY KEY, name VARCHAR(50));
   INSERT INTO users VALUES (1, 'Ajay');
   SELECT * FROM users;
   ```

**Sample Output**

![image](https://github.com/user-attachments/assets/0bd4e8a2-58cd-457a-bde7-6cc6ec69ade5)
 
2. Delete the MySQL pod:
   ```bash
   kubectl delete pod mysql-0
   ```

**Sample Output**

![image](https://github.com/user-attachments/assets/516fe17b-924f-4530-8931-fc80ee5eda1b)

3. Check if data is still present after the pod restarts:
   ```bash
   kubectl exec -it mysql-0 -- mysql -u root -p -e "USE mydb; SELECT * FROM users;"
   ```

**Sample Output**

![image](https://github.com/user-attachments/assets/6f4457b1-2d87-46d1-841c-ddaba69ac381)

--- 

## **How to back up and restore a MySQL database running inside a Kubernetes StatefulSet?**

To back up and restore a MySQL database running inside a **Kubernetes StatefulSet**, follow these steps:  

---

## **Step 1: Connect to the MySQL Pod**
First, get the MySQL pod name:  
```sh
kubectl get pods -l app=mysql
```
**Sample Output**

![image](https://github.com/user-attachments/assets/d723bcf4-0bc0-4035-b5f4-882f795cbf66)

Now, **exec** into the MySQL pod:
```sh
kubectl exec -it mysql-0 -- bash
```

**Sample Output**

![image](https://github.com/user-attachments/assets/f589a355-ee36-48b8-8fa4-d52d39861d0a)

Once inside the pod, log in to MySQL using the root password:
```sh
mysql -u root -p
```

**Sample Output**

![image](https://github.com/user-attachments/assets/82007b55-7bc4-4231-b9b0-0a9181c405d1)

Enter the **MYSQL_ROOT_PASSWORD** (stored in the Kubernetes Secret).  

---

## **Step 2: Take a Backup of the Database**
Run the following **mysqldump** command inside the MySQL pod to back up the database:
```sh
mysqldump -u root -p mydb > /var/lib/mysql/mydb_backup.sql
```
**Sample Output**

![image](https://github.com/user-attachments/assets/fa9a8ea5-fb90-48df-af69-90f4b4db503a)


Exit the MySQL shell:
```sh
exit
```
**Sample Output**

![image](https://github.com/user-attachments/assets/c9529c24-e875-4f79-b427-ba9de0214c8d)

Copy the backup file to your local system:
```sh
kubectl cp mysql-0:/var/lib/mysql/mydb_backup.sql ./mydb_backup.sql
```
**Sample Output**

![image](https://github.com/user-attachments/assets/85af7081-b6fb-4a25-9bf3-cb78f02666cc)

---

## **Step 3: Delete the Database**
Log back into MySQL:
```sh
kubectl exec -it mysql-0 -- mysql -u root -p
```
**Sample Output**

![image](https://github.com/user-attachments/assets/0d767c36-3aaa-451e-9a00-11e6aac9c9c1)

Drop the database:
```sql
DROP DATABASE mydb;
SHOW DATABASES;
```
**Sample Output**

![image](https://github.com/user-attachments/assets/7cad155b-a4b0-46ee-9f75-34d4f0cf01c1)

Exit MySQL:
```sh
exit
```
**Sample Output**

![image](https://github.com/user-attachments/assets/c9529c24-e875-4f79-b427-ba9de0214c8d)

---

## **Step 4: Restore the Database from Backup**
Create a new database:
```sh
kubectl exec -it mysql-0 -- mysql -u root -p -e "CREATE DATABASE mydb;"
```

**Sample Output**

![image](https://github.com/user-attachments/assets/ed4f6464-cf5b-4c2b-b4dd-2c79e4ae1da9)

Restore the backup:
```sh
kubectl cp ./mydb_backup.sql mysql-0:/var/lib/mysql/mydb_backup.sql
kubectl exec -it mysql-0 -- bash -c "mysql -u root -p mydb < /var/lib/mysql/mydb_backup.sql"
```

**Sample Output**

![image](https://github.com/user-attachments/assets/001c067e-6205-49b0-9f09-0377570294bd)

---

## **Step 5: Verify the Restoration**
Log into MySQL again:
```sh
kubectl exec -it mysql-0 -- mysql -u root -p
```

**Sample Output**

![image](https://github.com/user-attachments/assets/dcd76cdf-c92c-4a04-853c-6999fdb00ca2)

Check if the database and tables exist:
```sql
SHOW DATABASES;
USE mydb;
SELECT * FROM users;
```

**Sample Output**

![image](https://github.com/user-attachments/assets/dcbad2ee-c84c-49fa-9204-265304d94182)

If everything looks good, exit MySQL:
```sh
exit
```

**Sample Output**

![image](https://github.com/user-attachments/assets/c9529c24-e875-4f79-b427-ba9de0214c8d)

**Done!** The database has been backed up, deleted, and restored successfully. 
