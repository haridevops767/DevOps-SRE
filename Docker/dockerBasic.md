### What is Docker?
Docker is an open-source containerization platform that helps to create, deploy, and run applications in containers. A docker container is a lightweight package that has it's own dependencies, including bins and frameworks.

### How to Practice Docker in an EC2 Ubuntu 22 Instance?

We will go step by step:

#### **Step 1: Set Up the EC2 Instance**
1. Launch an **Ubuntu 22.04** EC2 instance.
2. Connect to the instance using SSH:
   ```bash
   ssh -i your-key.pem ubuntu@your-ec2-ip
   ```
3. Update the system:
   ```bash
   sudo apt update && sudo apt upgrade -y
   ```

#### **Step 2: Install Official Docker on Ubuntu 22**
1. Install necessary dependencies:
   ```bash
   sudo apt install -y ca-certificates curl gnupg
   ```
2. Add the Docker GPG key:
   ```bash
   sudo install -m 0755 -d /etc/apt/keyrings
   curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo tee /etc/apt/keyrings/docker.asc > /dev/null
   sudo chmod a+r /etc/apt/keyrings/docker.asc
   ```
3. Add the Docker repository:
   ```bash
   echo \
     "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
     $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
   ```
4. Update and install Docker:
   ```bash
   sudo apt update
   sudo apt install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
   ```
5. Verify the installation:
   ```bash
   docker --version
   ```

6. Start and enable Docker:
   ```bash
   sudo systemctl start docker
   sudo systemctl enable docker
   ```

7. Allow the current user to run Docker without sudo:
   ```bash
   sudo usermod -aG docker $USER
   ```

8. Log out and log back in for the group changes to take effect.

---
#### **Install Unofficial Docker on Ubuntu 22**
we can install unofficial docker by using 
```bash
apt install docker.io -y
```
**Sample Output**

![image](https://github.com/user-attachments/assets/a6af2f2f-985c-4e72-9d48-c2ad6dcf821f)

### **Step 1: Understanding and Running Docker Commands**  
Before diving into containers, let's start with the most basic Docker commands.

#### **1. Verify Docker Installation**  
Check if Docker is installed and running:
```bash
docker --version
docker info
```
**Sample Output**

![image](https://github.com/user-attachments/assets/6d1d4da2-8f52-4b11-b0ee-824fbe879098)

#### **2. Running Your First Container**
Run a simple container using the **hello-world** image:
```bash
docker run hello-world
```
**Sample Output**

![image](https://github.com/user-attachments/assets/bb7df06b-601f-4194-8041-bf31025b5e87)
![image](https://github.com/user-attachments/assets/73ca9307-33a7-4d93-87c9-50e8371faec5)

 **Explanation:**  
- Docker will **pull the image** from Docker Hub if it’s not available locally.  
- It creates a **container** from the image and executes it.  
- The message in the output confirms that Docker is installed and working.  

#### **3. Listing Running Containers**
Check active containers:
```bash
docker ps
```
**Sample Output**

![image](https://github.com/user-attachments/assets/a78821bd-ceae-4edd-a06b-34a507655885)

Check all containers (including stopped ones):
```bash
docker ps -a
```
**Sample Output**

![image](https://github.com/user-attachments/assets/0a854afa-f20e-4918-bc51-6b491d87dd42)

 **Explanation:**  
- `docker ps` → Shows currently running containers.  
- `docker ps -a` → Shows all containers, including stopped ones.

---

### **Step 2: Working with Docker Images**  
Docker containers are created from images. Let’s learn how to manage images.

#### **4. Search for Docker Images**
You can search for images from Docker Hub:
```bash
docker search nginx
```
**Sample Output**

![image](https://github.com/user-attachments/assets/bbd5b1cf-4032-4e5e-8973-8a76d788b173)

- This lists available Nginx images.

#### **5. Pull a Docker Image**
Download an image from Docker Hub:
```bash
docker pull nginx
```
**Sample Output**

![image](https://github.com/user-attachments/assets/0bf46323-8dbd-4bad-975a-58e8a4ea0d6c)

- This pulls the latest Nginx image.

#### **6. List Available Images**
Check images stored locally:
```bash
docker images
```
**Sample Output**

![image](https://github.com/user-attachments/assets/5fba70ca-1d6a-4547-ab90-876448626f58)

- This shows all downloaded images.

---

### **Step 3: Running and Managing Containers**  
Now, let’s create and manage containers.

#### **7. Running a Container**
Run an nginx container in detach mode:
```bash
docker run -itd --name nginx -p 80:80 nginx
```
**Sample Output**

![image](https://github.com/user-attachments/assets/65833eb4-02e0-497a-a246-805b7ea00d27)
![image](https://github.com/user-attachments/assets/ef027299-75e4-4c38-86c7-c92c2ff9e189)

#### **8. Restart a Stopped Container**
First, list all containers:
```bash
docker ps -a
```
Start a stopped container (replace `<container_id>` with your container's ID):
```bash
docker start <container_id>
```
**Sample Output**

![image](https://github.com/user-attachments/assets/d4be5a42-85c2-4fce-91c4-6a6f4c2c785e)

---

### **Step 4: Managing Containers**  
Containers can be started, stopped, removed, or deleted.

#### **9. Stop a Running Container**
```bash
docker stop <container_id>
```
**Sample Output**

![image](https://github.com/user-attachments/assets/c6b8ceec-512a-47bf-be3b-5488bd2e586f)

#### **10. Remove a Stopped Container**
```bash
docker rm <container_id>
```
**Sample Output**

![image](https://github.com/user-attachments/assets/ac364ee4-bfea-4555-8147-0ff02bc54e3d)

#### **11. Remove an Image**
```bash
docker rmi ubuntu
```
**Sample Output**

![image](https://github.com/user-attachments/assets/2607205b-57ea-44a4-bb73-7219156daedd)

---

### **Step 5: Running Containers in Detached Mode**  
Run an Nginx web server in the background:
```bash
docker run -d -p 8080:80 nginx
```
**Sample Output**

![image](https://github.com/user-attachments/assets/8fe22c33-bf84-4ac4-8731-bc88fd7ab5e6)

 **Explanation:**  
- `-d` → Detached mode (runs in the background).  
- `-p 8080:80` → Maps **port 80 (container)** to **port 8080 (host)**.  
- `nginx` → Image name.

Check running containers:
```bash
docker ps
```
**Sample Output**

![image](https://github.com/user-attachments/assets/04c45d27-bfdc-4d62-98c6-99aa54aea72f)

Access the nginx container using:
```bash
curl http://localhost:8080
```
**Sample Output**

![image](https://github.com/user-attachments/assets/ac965c5a-c683-4bb2-a664-f0d2fdae5263)

```bash
http://13.232.94.69:8080/
```
**Sample Output**

![image](https://github.com/user-attachments/assets/51facad2-24ad-46d1-bf16-0f4264b15858)

Stop and remove the container:
```bash
docker stop <container_id>
docker rm <container_id>
```
**Sample Output**

![image](https://github.com/user-attachments/assets/8003a3c8-3e90-466b-b936-1d757db20131)

---

## **Step 6: Executing Commands Inside a Running Container**
Once a container is running, you may need to interact with it.

### **12. Attach to a Running Container**
Attach to the container’s active session:
```sh
docker attach <container_id>
```
**Sample Output**

![image](https://github.com/user-attachments/assets/8fe2e56b-9229-400c-8488-4aa90e50cd3e)

To detach from the container, use `CTRL + P + Q` without stopping it.

### **13. Execute Commands in a Running Container**
Execute a command inside a running container:
```sh
docker exec -it <container_id> bash
```
Example:
```sh
docker exec -it nginx bash
```
(For Alpine-based images, use `/bin/sh` instead of `bash`.)

**Sample Output**

![image](https://github.com/user-attachments/assets/eaa7a86c-a23b-473f-af3a-f032b3ed9d23)

---

## **Step 7: Container Logs & Monitoring**
### **14. View Container Logs**
To check logs of a running container:
```sh
docker logs <container_id>
```
To follow live logs:
```sh
docker logs -f <container_id>
```
**Sample Output**

![image](https://github.com/user-attachments/assets/11d57806-bc03-4fc3-b86b-eb09c7a1b3b3)

### **15. Check Container Resource Usage**
Monitor a running container’s resource usage:
```sh
docker stats
```
**Sample Output**

![image](https://github.com/user-attachments/assets/7e01ac51-037c-473b-8272-8d073333aa10)

---

## **Step 8: Container Networking**
### **16. List Networks**
```sh
docker network ls
```
It shows available networks (`bridge`, `host`, `none`, etc.).

**Sample Output**

![image](https://github.com/user-attachments/assets/cffede15-94c5-44da-8dc1-66071e14ef33)

### **17. Create a Custom Network**
```sh
docker network create my-network
```
**Sample Output**

![image](https://github.com/user-attachments/assets/bf5a4aad-b321-45b3-a4e2-043da550abf0)

### **18. Run a Container in a Custom Network**
```sh
docker run -d --name web-server --network=my-network nginx
```
**Sample Output**

![image](https://github.com/user-attachments/assets/95cc9dbc-e9e3-4b66-bb49-77f3f9ee4093)

### **19. Inspect a Network**
```sh
docker network inspect my-network
```

**Sample Output**

![image](https://github.com/user-attachments/assets/f4ccec97-6f62-4bc6-966f-838e0de4df0e)

---

## **Step 9: Docker Volumes & Data Persistence**
By default, when a container is removed, its data is lost. **Volumes** help persist data.

### **20. Create and Use a Volume**
Create a volume:
```sh
docker volume create my-data
```

**Sample Output**

![image](https://github.com/user-attachments/assets/f65ef77f-7df8-4091-a292-cadf3870552c)
Run a container with a volume:
```sh
docker run -d -v my-data:/usr/share/nginx/html --name nginx-container nginx
```
**Sample Output**

![image](https://github.com/user-attachments/assets/8cd38dde-b067-4178-875e-d4483c0f7241)

### **21. List Volumes**
```sh
docker volume ls
```
**Sample Output**

![image](https://github.com/user-attachments/assets/f7645d6e-18e8-4e95-94fa-0fa2e66f9b18)

### **22. Inspect a Volume**
```sh
docker volume inspect my-data
```
**Sample Output**

![image](https://github.com/user-attachments/assets/0a43be20-a4aa-451b-af11-4bc8bf366886)

### **23. Remove a Volume**
```sh
docker volume rm my-data
```
**Sample Output**

![image](https://github.com/user-attachments/assets/6fb60105-48a2-4af1-a74d-4b76ecb38114)

---

