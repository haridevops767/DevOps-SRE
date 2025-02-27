# Jenkins Pipeline for Java based application using Maven, SonarQube, Argo CD, Helm and Kubernetes

![Screenshot 2023-03-28 at 9 38 09 PM](https://user-images.githubusercontent.com/43399466/228301952-abc02ca2-9942-4a67-8293-f76647b6f9d8.png)


Here are the step-by-step details to set up an end-to-end Jenkins pipeline for a Java application using SonarQube, Argo CD, Helm, and Kubernetes:

Prerequisites:

   - Java application code hosted on a Git repository
   - Jenkins server
   - SonarQube
   - Kubernetes cluster
   - Argo CD

## AWS EC2 Instance

#### **1. Cloud Infrastructure**  
- **Instance Type**: AWS EC2 (t2.medium or higher)  
- **OS**: Ubuntu 20.04 or more 
- **Security Groups**: Open required ports

### Install Jenkins.

Pre-Requisites:
 - Java (JDK)

### Run the below commands to install Java and Jenkins

Install Java

```
sudo su -
apt update && apt upgrade -y
apt install openjdk-17-jre
```

Verify Java is Installed

```
java -version
```

Now, you can proceed with installing Jenkins

```
curl -fsSL https://pkg.jenkins.io/debian/jenkins.io-2023.key | sudo tee \
  /usr/share/keyrings/jenkins-keyring.asc > /dev/null
echo deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] \
  https://pkg.jenkins.io/debian binary/ | sudo tee \
  /etc/apt/sources.list.d/jenkins.list > /dev/null
sudo apt-get update
sudo apt-get install jenkins
```

**Note: ** By default, Jenkins will not be accessible to the external world due to the inbound traffic restriction by AWS. Open port 8080 in the inbound traffic rules as show below.

- EC2 > Instances > Click on <Instance-ID>
- In the bottom tabs -> Click on Security
- Security groups
- Add inbound traffic rules as shown in the image (you can just allow TCP 8080 as well, in my case, I allowed `All traffic`).

<img width="1187" alt="Screenshot 2023-02-01 at 12 42 01 PM" src="https://user-images.githubusercontent.com/43399466/215975712-2fc569cb-9d76-49b4-9345-d8b62187aa22.png">


### Login to Jenkins using the below URL:

http://<ec2-instance-public-ip-address>:8080    [You can get the ec2-instance-public-ip-address from your AWS EC2 console page]

Note: If you are not interested in allowing `All Traffic` to your EC2 instance
      1. Delete the inbound traffic rule for your instance
      2. Edit the inbound traffic rule to only allow custom TCP port `8080`
  
After you login to Jenkins, 
      - Run the command to copy the Jenkins Admin Password - `sudo cat /var/lib/jenkins/secrets/initialAdminPassword`
      - Enter the Administrator password
      
<img width="1291" alt="Screenshot 2023-02-01 at 10 56 25 AM" src="https://user-images.githubusercontent.com/43399466/215959008-3ebca431-1f14-4d81-9f12-6bb232bfbee3.png">

### Click on Install suggested plugins

<img width="1291" alt="Screenshot 2023-02-01 at 10 58 40 AM" src="https://user-images.githubusercontent.com/43399466/215959294-047eadef-7e64-4795-bd3b-b1efb0375988.png">

Wait for the Jenkins to Install suggested plugins

<img width="1291" alt="Screenshot 2023-02-01 at 10 59 31 AM" src="https://user-images.githubusercontent.com/43399466/215959398-344b5721-28ec-47a5-8908-b698e435608d.png">

Create First Admin User or Skip the step [If you want to use this Jenkins instance for future use-cases as well, better to create admin user]

<img width="990" alt="Screenshot 2023-02-01 at 11 02 09 AM" src="https://user-images.githubusercontent.com/43399466/215959757-403246c8-e739-4103-9265-6bdab418013e.png">

Jenkins Installation is Successful. You can now starting using the Jenkins 

<img width="990" alt="Screenshot 2023-02-01 at 11 14 13 AM" src="https://user-images.githubusercontent.com/43399466/215961440-3f13f82b-61a2-4117-88bc-0da265a67fa7.png">

## Install the Docker Pipeline plugin in Jenkins:

   - Log in to Jenkins.
   - Go to Manage Jenkins > Manage Plugins.
   - In the Available tab, search for "Docker Pipeline".
   - Select the plugin and click the Install button.
   - Restart Jenkins after the plugin is installed.
   
<img width="1392" alt="Screenshot 2023-02-01 at 12 17 02 PM" src="https://user-images.githubusercontent.com/43399466/215973898-7c366525-15db-4876-bd71-49522ecb267d.png">

Wait for the Jenkins to be restarted.


## Docker Slave Configuration

Run the below command to Install Docker

```
sudo apt update
sudo apt install docker.io
```
 
### Grant Jenkins user and Ubuntu user permission to docker deamon.

```
sudo su - 
usermod -aG docker jenkins
usermod -aG docker ubuntu
systemctl restart docker
```

Once you are done with the above steps, it is better to restart Jenkins.

```
http://<ec2-instance-public-ip>:8080/restart
```

The docker agent configuration is now successful.

---

### Create pipeline in jenkins 

click on new item --> and type name as sample-project ---> select pipeline --->      click on ok 
    
![image](https://github.com/user-attachments/assets/41fb8c0e-30a5-42c5-9f4e-1b172ef8d1ff)

select pipeline script from SCM --> and provide git repository url and branch name, jenkinsfile script path --> click on apply and save like below screenshots

![image](https://github.com/user-attachments/assets/dc161fcd-64a6-4819-a68e-fb6b273ccdf8)
![image](https://github.com/user-attachments/assets/0f96b286-0881-47b0-8727-640bd3224898)

## Next Steps

goto jenkins UI --> manage jenkins --> manage plugins --> available plugins --> install sonar scanner plugin like below 

![image](https://github.com/user-attachments/assets/299ac7d0-a606-4a87-b09e-71b11e8b8299)

### Configure a Sonar Server

```
apt install unzip
adduser sonarqube
sudo su - sonarqube
wget https://binaries.sonarsource.com/Distribution/sonarqube/sonarqube-9.4.0.54424.zip
unzip sonarqube-9.4.0.54424.zip
chmod -R 755 /home/sonarqube/sonarqube-9.4.0.54424
chown -R sonarqube:sonarqube /home/sonarqube/sonarqube-9.4.0.54424
cd sonarqube-9.4.0.54424/bin/linux-x86-64/
./sonar.sh start
```

Now you can access the `SonarQube Server` on `http://3.109.132.112:9000` 

![image](https://github.com/user-attachments/assets/ab857d7d-6842-4118-9f9b-5656275a23c6)

- next step is need to create sonar token
  
  goto sonarqube UI --> click on administrator --> my account --> click on security --> token section give name as sonar-token and click on generate token.

- goto jenkins UI and click on manage jenkins --> click on credentials --> click on system --> click on global credentials --> add credentials --> add sonar-token here
![image](https://github.com/user-attachments/assets/85f72d0d-d2ff-4bd4-925a-3a772855334d)
- as well as add your dockerhub credentials
![image](https://github.com/user-attachments/assets/f81d2227-08b6-4bff-8c59-06a70961f4a4)
- add your github credentials
![image](https://github.com/user-attachments/assets/d5a92776-01d2-4afe-9e5b-bbd43cc36a9a)
- afterthat restart once your jenkins server
http://13.201.70.86:8080/restart
## **Install K3s**
 **Run the K3s Installer on server**  
```bash
curl -sfL https://get.k3s.io | sh -s - --write-kubeconfig-mode 644
```
 **Check if the cluster is running**  
```bash
sudo kubectl get nodes
```
![image](https://github.com/user-attachments/assets/f4f7782a-1a9b-47a1-b223-6f101357e6c4)

## **Install kubernetes controllers using Operators**

Install on Kubernetes

Install Operator Lifecycle Manager (OLM), a tool to help manage the Operators running on your cluster.

```bash
curl -sL https://github.com/operator-framework/operator-lifecycle-manager/releases/download/v0.31.0/install.sh | bash -s v0.31.0
```
![image](https://github.com/user-attachments/assets/ac717cb5-2f75-48a8-a0e8-bb808ff0b12a)

Install the operator by running the following command:

```bash
kubectl create -f https://operatorhub.io/install/argocd-operator.yaml
```
![image](https://github.com/user-attachments/assets/9e918151-d5e8-4099-94a3-76f77b9007ad)

This Operator will be installed in the "operators" namespace and will be usable from all namespaces in the cluster.

After install, watch your operator come up using next command.

```bash
kubectl get csv -n operators
```
To use it, checkout the custom resource definitions (CRDs) introduced by this operator to start using it.

![image](https://github.com/user-attachments/assets/9454fbbf-ca1d-4df0-9450-336fdf89bacf)

```bash
kubectl get pods -n operators
```
![image](https://github.com/user-attachments/assets/dfe0902a-397a-4b4e-b1d8-0b7786fac4b1)

## Open github repo and modify jenkinsfile like sonar ipaddress,etc.., and save jenkinsfile

 next step is open jenkins UI and click on build option.

 ![image](https://github.com/user-attachments/assets/6bc6ee21-7446-4226-9139-c9509f1fad51)

## **ArgoCD** 
vi argocd-basic.yaml

```bash
apiVersion: argoproj.io/v1alpha1
kind: ArgoCD
metadata:
  name: example-argocd
  labels:
    example: basic
spec: {}
```

```bash
kubectl create -f argocd-basic.yaml
kubectl get pods
```
![image](https://github.com/user-attachments/assets/9ead6967-f0b4-4910-abfd-a9ba198d1835)

```bash
kubectl get svc
```
![image](https://github.com/user-attachments/assets/6214fc66-a1a6-4731-a778-122c313c5e5a)

```bash
kubectl edit svc example-argocd-server
```
edit type: NodePort and save :wq!
```bash
kubectl get svc
```
![image](https://github.com/user-attachments/assets/103d048a-5e9e-4de4-b729-44057374a4e3)

now, you can access argocd UI using server public ip and followed by NodePort
```bash
http://43.205.125.162:30376
```
![image](https://github.com/user-attachments/assets/ae1dcb4b-4004-427d-ade6-5e6048ce5892)

## to know argocd password follow below steps

```bash
kubectl get secret
kubectl edit secret example-argocd-cluster
```
![image](https://github.com/user-attachments/assets/ffe85e65-4896-48f5-ba66-d2cdc801b2de)

copy the root password and convert it to base64 like below
```bash
echo ME5RelliSHU0bzV5Y0dTVXdwSjltWmp0ZnhYVElXN24= | base64 -d
```
![image](https://github.com/user-attachments/assets/1ec2a415-fb48-4ef7-915a-1e7bfc6f3145)

Now, using above password you can login to argocd like below

![image](https://github.com/user-attachments/assets/92a760a0-b9ce-44a6-a7dc-27384b4eeccf)

![image](https://github.com/user-attachments/assets/14b80327-9f2d-4108-ba4c-9502f0103319)

now, in argocd UI click on create new app and provide required fields correctly and click on create and click on sync app

![image](https://github.com/user-attachments/assets/0c762a89-49bc-4632-a6da-3104c1a79805)
![image](https://github.com/user-attachments/assets/561000e8-78cf-42a7-9d49-1dff2446b9d9)

---



