### **How to scan docker image using Trivy on EC2 Ubuntu 22**  

To practice **Docker image vulnerability scanning** using **Trivy**, follow these steps:  

---

## **1. Prerequisites**  
Before installing **Trivy**, ensure you have:  

- An **EC2 Ubuntu 22.04 instance** (running and accessible via SSH)  
- **Docker installed and running**  
  - If Docker is not installed, use:  
    ```bash
    sudo apt update -y
    sudo apt install docker.io -y
    sudo systemctl start docker
    sudo systemctl enable docker
    ```
- A **basic Docker image** to scan (such as `nginx`, `ubuntu`, or a custom-built image)

**Sample Output**

![image](https://github.com/user-attachments/assets/4db5f1e2-b341-439f-9ed6-b1eb02fbc8bf)

---

## **2. Install Trivy on Ubuntu 22**  
Run the following commands to install Trivy:  

```bash
sudo apt update -y
sudo apt install wget -y
wget https://github.com/aquasecurity/trivy/releases/download/v0.59.1/trivy_0.59.1_Linux-64bit.deb
sudo dpkg -i trivy_0.59.1_Linux-64bit.deb
```
**Sample Output**

![image](https://github.com/user-attachments/assets/6c90fae0-aec7-4237-a80d-e46bd112694c)

Verify the installation:  
```bash
trivy --version
```
**Sample Output**

![image](https://github.com/user-attachments/assets/44219928-d517-43a3-9b84-a50cacb9d33f)

---

## **3. Scan a Docker Image for Vulnerabilities**  
Once Trivy is installed, you can scan any Docker image.

### **Example: Scan the Nginx Image**
1. Pull the image if not already available:  
   ```bash
   docker pull nginx:latest
   ```
   **Sample Output**

   ![image](https://github.com/user-attachments/assets/0fd17448-560e-4360-b64b-74293c9b6b95)

2. Run Trivy to scan the image:  
   ```bash
   trivy image nginx:latest
   ```
   
This will analyze the **nginx:latest** image and display **vulnerabilities (CVEs)** found in system packages and libraries.

**Sample Output**

![image](https://github.com/user-attachments/assets/9fd86256-158e-415f-8726-83bf9cf30e3b)

Based on the image, the `nginx:latest` Docker image has been scanned using **Trivy**, and it shows:  
- **Total vulnerabilities**: **154**  
  - **Low:** 100  
  - **Medium:** 44  
  - **High:** 8  
  - **Critical:** 2  

### **Steps to Resolve Docker Image Vulnerabilities:**

#### **1. Use a More Secure Base Image**  
- The scan shows that **nginx:latest** is based on **Debian 12.9**.  
- Try using an **official lightweight and secure variant**, such as **nginx:alpine**, which has fewer vulnerabilities.

```bash
docker pull nginx:alpine
```
**Sample Output**

![image](https://github.com/user-attachments/assets/2bdf34dd-d925-4850-b8a0-798ac1b100d9)
![image](https://github.com/user-attachments/assets/a7b07912-a1eb-43ad-ba2c-7869e550e61e)

- Then scan again:
```bash
trivy image nginx:alpine
```
**Sample Output**

![image](https://github.com/user-attachments/assets/170d1979-9d07-43c2-957b-698fd4b7e9bf)

Based on above screenshot it shows **zero vulnerabilities** for the `nginx:alpine` image. This confirms that using **Alpine-based images** is a better security practice compared to Debian-based images like `nginx:latest`.  

### **Key Takeaways:**  
- **Alpine Linux** is a **lightweight and minimalistic** OS with fewer vulnerabilities.  
- Debian-based images (`nginx:latest`) often contain **more system utilities**, increasing the attack surface.  
- Always prefer **`nginx:alpine`** if security and minimal size are priorities.  

### **What’s Next?**  
1. If your application doesn't require Debian-specific dependencies, **stick to `nginx:alpine`**.  
2. If you must use Debian, periodically **update packages** and **scan for vulnerabilities**.  
3. Consider **multi-stage builds** to remove unnecessary dependencies.  

---

#### **2. Keep the Image Updated**  
- Ensure that you are using the latest **patched** version:
```bash
docker pull nginx:latest
```
- Check for available updates on [Docker Hub](https://hub.docker.com/_/nginx).

---

#### **3. Apply OS and Package Updates**  
If you **must** use `nginx:latest`, create a **Dockerfile** to update OS packages before running the container:

```dockerfile
FROM nginx:latest
RUN apt-get update && apt-get upgrade -y && apt-get clean
```
Then, rebuild and scan the image:
```bash
docker build -t my-secure-nginx .
```
```bash
trivy image my-secure-nginx
```
**Sample Output**

![image](https://github.com/user-attachments/assets/fea8fac0-e38f-4c79-8e78-48cca9d9b4a3)

---

## **4. Scan a Running Container**  
To scan an already running container, follow these steps:

1. Start a container:  
   ```bash
   docker run -d --name mynginx nginx:latest
   ```
2. Get the container ID:  
   ```bash
   docker ps
   ```
3. Scan the container using Trivy:  
   ```bash
   trivy --input $(docker inspect --format="{{.Id}}" mynginx)
   ```

---

## **5. Scan a Remote Image**  
If you want to scan an image from Docker Hub without pulling it locally, run:  
```bash
trivy image --no-progress nginx:latest
```

---

## **6. Scan Filesystem for Vulnerabilities**  
Trivy can also scan local directories for vulnerabilities:  
```bash
trivy fs /etc
```

---

## **7. Schedule Trivy Scans (Optional)**  
To automate security scans, create a **cron job**:  
```bash
echo "0 3 * * * trivy image nginx:latest > /var/log/trivy.log" | sudo tee -a /etc/crontab
```
This will run a Trivy scan daily at **3 AM** and save the results in `/var/log/trivy.log`.

---

## **8. Best Practices**  
- Keep Trivy updated:  
  ```bash
  sudo apt update && sudo apt upgrade trivy -y
  ```
- Regularly scan images before deploying them in production.  
- Use **Trivy with CI/CD pipelines** (such as GitHub Actions, Jenkins, or GitLab CI).  

---
