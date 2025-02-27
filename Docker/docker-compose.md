### What is Docker Compose?  
Docker Compose is a tool used to define and run multi-container Docker applications. Instead of running multiple `docker run` commands, you can define all your containers, networks, and volumes in a single **YAML file** (`docker-compose.yml`) and start them with a single command.  

It simplifies container management, especially when working with multiple services like databases, APIs, and frontends.  

---

### Key Concepts in Docker Compose:
1. **Services**: Define containers in `docker-compose.yml`. Each service runs a container.
2. **Networks**: Allows communication between services.
3. **Volumes**: Used for persistent storage.
4. **Environment Variables**: Used for configuration.

---

To install Docker Compose in Ubuntu, follow these steps:  

### **Step 1: Update the System**  
Run the following command to update the package list:  
```bash
sudo apt update && sudo apt upgrade -y
```

### **Step 2: Download Docker Compose**  
Use the command below to download the latest stable version:  
```bash
sudo curl -L "https://github.com/docker/compose/releases/latest/download/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
```

### **Step 3: Give Execution Permission**  
After downloading, set the correct permissions:  
```bash
sudo chmod +x /usr/local/bin/docker-compose
```

### **Step 4: Verify the Installation**  
Check if Docker Compose is installed successfully:  
```bash
docker-compose --version
```
This will display the installed version of Docker Compose.

### **Alternative: Install via APT (Unofficial Method)**  
```bash
sudo apt install docker-compose -y
```
**Sample Output**

![image](https://github.com/user-attachments/assets/5af099ee-f256-4b5e-9802-921f02c7b13c)

This may install an older version, so using the first method is recommended.

### How to Practice Docker Compose in Ubuntu 22  

#### **Step 1: Create a Docker Compose YAML file**  
Create a directory and navigate to it:  
```bash
mkdir my-compose-app && cd my-compose-app
```
**Sample Output**

![image](https://github.com/user-attachments/assets/bb6b4df4-05cb-4a8c-932c-197a0aab44da)

Create a `docker-compose.yml` file:  
```bash
vi docker-compose.yml
```

Add the following simple example for running **Nginx**:  
```yaml
version: "3.8"

services:
  web:
    image: nginx:latest
    ports:
      - "8080:80"
```
Save and exit the file.

**Sample Output**

![image](https://github.com/user-attachments/assets/f0608656-3248-47bd-a4aa-6546b6a8c920)

---

#### **Step 2: Start the Container**  
Run the following command to start the container:  
```bash
docker-compose up -d
```
- `up`: Starts the containers.
- `-d`: Runs in detached mode (in the background).

**Sample Output**

![image](https://github.com/user-attachments/assets/82177f6c-4599-41e9-b39f-c6d4c69094fd)

---

#### **Step 3: Verify Running Containers**  
Check if the container is running:  
```bash
docker ps
```
**Sample Output**

![image](https://github.com/user-attachments/assets/5f27975e-c322-4b62-8cfb-76d5817c0fd3)

---

#### **Step 4: Access the Application**  
Open a browser and go to:  
```bash
http://your-ec2-public-ip:8080
```
You should see the **Nginx default page**.

**Sample Output**

![image](https://github.com/user-attachments/assets/484247df-08a7-43d6-96ee-dec37e62b9b2)

---

#### **Step 5: Stop and Remove Containers**  
To stop the running services:  
```bash
docker-compose down
```
**Sample Output**

![image](https://github.com/user-attachments/assets/7af26d42-ab2a-49c6-8509-2a0669d57178)

---
