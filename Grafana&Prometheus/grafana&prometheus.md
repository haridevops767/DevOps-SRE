# **How to Monitor Application Server Metrics Using Grafana & Prometheus?**

## **How to install Grafana in AWS Ubuntu spot instace**
Here’s a step-by-step guide to installing **Grafana** on an **AWS Ubuntu 22.04 Spot Instance** with all the prerequisites.  

---

### **Step 1: Launch an AWS Spot Instance (Ubuntu 22.04)**
1. Log in to **AWS Console**.
2. Navigate to **EC2** > **Instances** > **Launch Instances**.
3. Select **Ubuntu 22.04** as the OS.
4. Choose an **Instance Type** (t2.micro is enough for testing).
5. Under **Purchasing option**, select **Request Spot Instance**.
6. Configure **Security Group**:
   - Allow **Inbound Rules** for:
     - **SSH (22)**
     - **Grafana UI (3000)**
7. **Launch the instance** and connect via SSH.

---

### **Step 2: Connect to the Instance**
Use SSH to connect to the instance:
```bash
ssh -i your-key.pem ubuntu@your-instance-ip
```

---

### **Step 3: Update the System**
```bash
sudo apt update && sudo apt upgrade -y
```

---

### **Step 4: Install Dependencies**
Ensure required packages are installed:
```bash
sudo apt install -y software-properties-common apt-transport-https wget
```
**Sample Output**

![image](https://github.com/user-attachments/assets/bcf57296-4f87-4197-8bf1-d0454eb5b7f7)

---

### **Step 5: Add the Grafana Repository**
```bash
sudo mkdir -p /etc/apt/keyrings
wget -q -O - https://packages.grafana.com/gpg.key | sudo tee /etc/apt/keyrings/grafana.asc
echo "deb [signed-by=/etc/apt/keyrings/grafana.asc] https://packages.grafana.com/oss/deb stable main" | sudo tee /etc/apt/sources.list.d/grafana.list
```
**Sample Output**

![image](https://github.com/user-attachments/assets/aeba6c96-4be7-407d-88d1-ceda3b0ab133)

---

### **Step 6: Install Grafana**
```bash
sudo apt update
sudo apt install -y grafana
```
**Sample Output**

![image](https://github.com/user-attachments/assets/fb94350d-1257-4c2a-beff-0d026b5c14f2)

---

### **Step 7: Start & Enable Grafana Service**
```bash
sudo systemctl enable --now grafana-server
```
**Sample Output**

![image](https://github.com/user-attachments/assets/47a578fa-fcc5-453d-8ef2-1d6a446e687f)

Check if Grafana is running:
```bash
sudo systemctl status grafana-server
```
**Sample Output**

![image](https://github.com/user-attachments/assets/d28398fe-860b-47ff-a517-7ed2b6d718ae)

---

### **Step 8: Open Port 3000 in AWS Security Group**
1. Go to **EC2 Dashboard** > **Security Groups**.
2. Find your instance's **Security Group**.
3. Add an **Inbound Rule**:
   - **Type:** Custom TCP
   - **Port:** 3000
   - **Source:** 0.0.0.0/0 (or restrict it to your IP)

---

### **Step 9: Access Grafana Web Interface**
Open your browser and go to:
```
http://65.0.176.241:3000
```
**Sample Output**

![image](https://github.com/user-attachments/assets/c70e5458-6b38-4a9e-9080-85226392117e)

---

### **Step 10: Login to Grafana**
- **Default Username:** `admin`
- **Default Password:** `admin`
- Change the password on first login.
  
**Sample Output**

![image](https://github.com/user-attachments/assets/11a41854-4b7d-429c-b452-846dae693e9c)
![image](https://github.com/user-attachments/assets/1889fdfa-1b78-4ed3-b261-5666d9f3b3b6)
![image](https://github.com/user-attachments/assets/d6d9a747-3d76-473f-9427-1d318f7f573c)

---

### **Grafana Installation Completed**  
---
# **Next Step**
# **How to install Prometheus in EC2 Ubuntu instance** 

### **Install Prometheus on AWS EC2 Ubuntu 22.04 Spot Instance Using APT**  

---

## **Step 1: Install Prometheus**
Run the following command to install Prometheus using APT:

```bash
sudo apt update
sudo apt install prometheus -y
```
---

## **Step 2: Verify Installation**
Check if Prometheus is installed correctly:

```bash
prometheus --version
```
---

## **Step 3: Enable and Start Prometheus Service**
Since you installed Prometheus via APT, it should already have a systemd service. Enable and start it:

```bash
sudo systemctl enable prometheus
sudo systemctl start prometheus
```

Check if Prometheus is running:

```bash
systemctl status prometheus
```
**Sample Output**

![Screenshot 2025-02-15 172636](https://github.com/user-attachments/assets/314ef69a-0a9e-4a46-b429-c0c6abf3cb17)

## **Step 4: Access Prometheus**
Now, check if you can access Prometheus from your web browser:

```
http://65.0.176.241:9090
```

**Sample Output**

![image](https://github.com/user-attachments/assets/b9f16094-304b-4dfb-9878-864d672672c1)

---



## **Connect Prometheus to Grafana**
If you want to **visualize Prometheus metrics** in Grafana:
1. Log in to Grafana.
2. Grafana UI → **Data Sources** → **Add data source**.
3. Select **Prometheus**.
4. Set **Prometheu_URL** as:
   ```
   http://65.0.176.241:9090
   ```
5. Click **Save & Test**.

**Sample Output**

![image](https://github.com/user-attachments/assets/b0b334b3-f08f-4ebe-953a-6e97591f2170)
![image](https://github.com/user-attachments/assets/f28a252c-3434-4a54-8b51-1e20e2127a62)

Goto Dashboards -> click on new -> import -> provide grafana dashboard URL or ID as 1860 -> click on load

**Sample Output**

   ![image](https://github.com/user-attachments/assets/d5e49983-3d63-412b-81bc-b2557ddabcd3)

   ![image](https://github.com/user-attachments/assets/b13b293c-ebb8-414b-aa89-8b65bc6fce65)

After we need to select prometheus datasource like below

![image](https://github.com/user-attachments/assets/4115cad0-49eb-4b13-adb7-1181685a50c9)
![image](https://github.com/user-attachments/assets/91753894-35cd-41b6-90ee-eb8a6945109a)

Next click on Import Option -> when we click on Import it shows Application Server Metrics like CPU, RAM, Storage and etc.., like below.

![image](https://github.com/user-attachments/assets/ad1e6d1e-44e4-4961-b903-ab2107e6fb02)

# I can now monitor my application server’s health with real-time visualizations. This helps in troubleshooting performance issues quickly.
---

# Now that **Prometheus and Grafana** are set up for monitoring, let's **add another EC2 instance** for monitoring and **configure Gmail notifications** for alerts.  

---

# **Add Another EC2 Instance for Monitoring**
To monitor a new EC2 instance, we need to install **Node Exporter** on it and configure **Prometheus** to scrape its metrics.

## **Step 1: Install Node Exporter on the New EC2 Instance**
### **1.1 Connect to the New EC2 Instance**
SSH into the **new** EC2 instance:
```bash
ssh -i your-key.pem ubuntu@your-new-instance-ip
```

### **1.2 Download and Install Node Exporter**
```bash
VERSION="1.7.0"
wget https://github.com/prometheus/node_exporter/releases/download/v${VERSION}/node_exporter-${VERSION}.linux-amd64.tar.gz
tar -xvf node_exporter-${VERSION}.linux-amd64.tar.gz
sudo mv node_exporter-${VERSION}.linux-amd64/node_exporter /usr/local/bin/
node_exporter --version
```

**Sample Output**

![image](https://github.com/user-attachments/assets/9b83e07d-695e-4e54-be59-9343007d9693)

### **1.3 Start Node Exporter**
Run Node Exporter as a background process:
```bash
nohup node_exporter &
```

**Sample Output**

![image](https://github.com/user-attachments/assets/a89e3505-89f5-4144-a365-57d242d66213)

Check if it's running:
```bash
curl http://localhost:9100/metrics
```

**Sample Output**

![image](https://github.com/user-attachments/assets/d1cd0f85-0ee6-4b6c-a01d-9b003c834c8a)

You should see **metrics data** in the response.

---

## **Step 2: Configure Prometheus to Scrape Metrics from the New EC2 Instance**
### **2.1 SSH into the Prometheus Server**
```bash
ssh -i your-key.pem ubuntu@your-prometheus-server-ip
```
### **2.2 Edit Prometheus Configuration**
Open the Prometheus config file:
```bash
sudo nano /etc/prometheus/prometheus.yml
```

Add the **new EC2 instance's IP** under `scrape_configs`:
```yaml
scrape_configs:
  - job_name: 'node_exporter'
    static_configs:
      - targets: ['localhost:9100', '172.31.2.182:9100']
```

**Sample Output**

![image](https://github.com/user-attachments/assets/1f975fa4-8902-4016-9925-88c160aceea6)

 **Replace** `NEW_EC2_IP` with the actual IP of the new EC2 instance.

### **2.3 Restart Prometheus**
```bash
sudo systemctl restart prometheus
```

### **2.4 Verify if Prometheus is Scraping the New Instance**
```bash
curl http://localhost:9090/api/v1/targets
```
You should see the **new EC2 instance (http://15.207.100.15:9100) in "UP" status**.

**Sample Output**

![image](https://github.com/user-attachments/assets/653e219d-34b1-4de3-87f2-6382e5b9ba89)
![image](https://github.com/user-attachments/assets/8b1cf7c0-85c5-418b-aaa2-894a62db3edb)
![image](https://github.com/user-attachments/assets/210d38dd-f81c-44f1-8e12-e8290b241d49)

---

# **Set Up Gmail Notifications for Alerts**
## **Step 3: Configure Alertmanager for Email Alerts**
Prometheus **does not send alerts directly**. It uses **Alertmanager** to **send notifications to Gmail, Slack, etc.**.

### **3.1 Install Alertmanager**

### **Download Alertmanager with the Correct Version**
```bash
wget https://github.com/prometheus/alertmanager/releases/download/v0.28.0/alertmanager-0.28.0.linux-amd64.tar.gz
```

**Sample Output**

![image](https://github.com/user-attachments/assets/8dcc6a8d-1f2c-42fd-aa96-d1269061e378)

OR, using `curl`:
```bash
curl -LO https://github.com/prometheus/alertmanager/releases/download/v0.28.0/alertmanager-0.28.0.linux-amd64.tar.gz
```

After downloading, follow these steps:
```bash
tar -xvf alertmanager-0.28.0.linux-amd64.tar.gz
sudo mv alertmanager-0.28.0.linux-amd64/alertmanager /usr/local/bin/
alertmanager --version
```
**Sample Output**

![image](https://github.com/user-attachments/assets/576b354b-f5f2-4cad-9b3b-d3bc409eb339)

---

### **3.2 Create an Alertmanager Configuration File**

```bash
sudo mkdir -p /etc/alertmanager
```

```bash
sudo nano /etc/alertmanager/alertmanager.yml
```

Add the following:
```yaml
global:
  resolve_timeout: 5m
  smtp_smarthost: 'smtp.gmail.com:587'
  smtp_from: 'your-email@gmail.com'
  smtp_auth_username: 'your-email@gmail.com'
  smtp_auth_password: 'your-app-password'
  smtp_require_tls: true

route:
  group_by: ['alertname']
  receiver: 'email-notifications'

receivers:
  - name: 'email-notifications'
    email_configs:
      - to: 'your-email@gmail.com'
        send_resolved: true
```
 **Replace** `your-email@gmail.com` with your Gmail and  
**Replace** `your-app-password` with a **Gmail App Password** (not your actual Gmail password).

**Sample Output**

![image](https://github.com/user-attachments/assets/5b8b021f-a66c-4041-9014-721227e2fa14)

### **3.3 Start Alertmanager**
```bash
nohup alertmanager --config.file=/etc/alertmanager/alertmanager.yml &
```

**Sample Output**

![image](https://github.com/user-attachments/assets/e12ea702-5e69-4924-9bae-953496a2b9b0)

---

## **Step 4: Configure Prometheus to Use Alertmanager**
Edit the **Prometheus configuration**:
```bash
sudo nano /etc/prometheus/prometheus.yml
```

Add Alertmanager under `alerting`:
```yaml
alerting:
  alertmanagers:
    - static_configs:
        - targets: ['localhost:9093']
```

Restart Prometheus:
```bash
sudo systemctl restart prometheus
```

---

## **Step 5: Create an Alert Rule (e.g., High CPU Usage Alert)**
Create a new **alert rule file**:
```bash
sudo nano /etc/prometheus/alert-rules.yml
```

Add a **CPU usage alert**:
```yaml
groups:
  - name: high-cpu-usage
    rules:
      - alert: HighCPUUsage
        expr: rate(node_cpu_seconds_total{mode="user"}[5m]) * 100 > 80
        for: 1m
        labels:
          severity: critical
        annotations:
          summary: "High CPU Usage detected on {{ $labels.instance }}"
          description: "CPU usage is above 80% for more than 1 minute."
```

Modify `prometheus.yml` to load the alert rule:
```yaml
rule_files:
  - "/etc/prometheus/alert-rules.yml"
```

Restart Prometheus:
```bash
sudo systemctl restart prometheus
```

---

# **Final Steps**
### **1. Verify New EC2 Instance is Being Monitored**
Go to **Prometheus UI**:
```
http://<your-prometheus-server-ip>:9090
```
Run this **PromQL query** to see if the new instance is reporting:
```promql
up
```
It should return `1` for both **localhost:9100** and **NEW_EC2_IP:9100**.

---

### **2. Verify Alerts are Firing**
Check active alerts in Prometheus:
```
http://<your-prometheus-server-ip>:9090/alerts
```
If CPU crosses **80% usage**, the **alert will trigger**.

---

### **3. Verify Alertmanager is Sending Emails**
Go to Alertmanager UI:
```
http://<your-prometheus-server-ip>:9093
```
If an alert triggers, an **email notification will be sent** to your Gmail.

---

# **Summary**
- **Added a new EC2 instance for monitoring using Node Exporter**  
- **Configured Prometheus to scrape the new instance's metrics**  
- **Installed and configured Alertmanager**  
- **Set up Gmail SMTP for email alerts**  
- **Created an alert rule for high CPU usage**  

---
