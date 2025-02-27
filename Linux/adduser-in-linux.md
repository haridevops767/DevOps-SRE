### **Granting `ajay` User No Password `sudo` Access for `dmidecode` Command**  

This documentation provides step-by-step instructions to create a user named `ajay`, grant them sudo access, and configure it so they can run the `dmidecode` command without a password prompt.  

---

## **Step 1: Create a New User (`ajay`)**  
Create a new user named `ajay` and set up their environment.  
```sh
useradd -m -s /bin/bash ajay
```
- `-m` → Creates a home directory (`/home/ajay`).  
- `-s /bin/bash` → Sets Bash as the default shell.  

**Sample Output**

![image](https://github.com/user-attachments/assets/1f0e96d4-f08a-4045-b406-8246ddca0517)

```sh
passwd ajay
```
Enter a password when prompted.

**Sample Output**

![image](https://github.com/user-attachments/assets/fec74a9c-d340-4ae9-80f8-2e8b5bd8ca46)

Verify the user creation:
```sh
id ajay
```
**Sample Output**

![image](https://github.com/user-attachments/assets/84cf3e1b-8ce7-49c4-af3f-2ac8804c670c)

Check the `/etc/passwd` file to confirm the user exists:
```sh
cat /etc/passwd | grep -i ajay
```

**Sample Output**

![image](https://github.com/user-attachments/assets/124bbe2e-f101-48ad-8c76-2a89b942d6a2)

---

## **Step 2: Switch to `ajay` User and Verify**  
Use the `su` command to switch to `ajay`:
```sh
su - ajay
```
**Sample Output**

![image](https://github.com/user-attachments/assets/faca7242-f841-4ff5-ba38-86782ce5826f)

Now, check system hardware details with:
```sh
dmidecode
```
**Sample Output**

![image](https://github.com/user-attachments/assets/e45aa5df-48d9-41a3-a0d5-4c181feff78a)

By default, `dmidecode` requires sudo access. Check its location:
```sh
which dmidecode
```
**Sample Output**

![image](https://github.com/user-attachments/assets/214d295c-5c87-403c-8c8f-6ac8e2b12d68)

Exit back to the root user:
```sh
exit
```
**Sample Output**

![image](https://github.com/user-attachments/assets/72962ef9-f1e4-4a1b-82b4-073e75e473bd)

---

## **Step 3: Grant No Password `sudo` Access to `ajay` for `dmidecode`**  
Edit the `sudoers` file using:
```sh
visudo
```
At the end of the file, add this line:  
```sh
ajay ALL=(ALL) NOPASSWD:/usr/sbin/dmidecode
```
Save and exit (`CTRL+X`, then `Y`, then `Enter`).  

**Sample Output**

![image](https://github.com/user-attachments/assets/24ae202d-4a22-4ae8-b8c1-2d672fb6fc4f)

Verify `ajay`'s sudo permissions:
```sh
sudo -l
```
**Sample Output**

![image](https://github.com/user-attachments/assets/a6c838ec-7821-4ba2-9ce5-bee10082266e)

---

## **Step 4: Test `dmidecode` Execution Without Password**  
Switch to `ajay`:
```sh
su - ajay
```
**Sample Output**

![image](https://github.com/user-attachments/assets/5b38bd08-34aa-4d5d-9a4d-f85b5d829bca)

Check sudo permissions:
```sh
sudo -l
```
**Sample Output**

![image](https://github.com/user-attachments/assets/66ef5a75-312b-4bf8-b940-0e45bf85b1ac)

Run `dmidecode` without a password:
```sh
sudo dmidecode
```
**Sample Output**

![image](https://github.com/user-attachments/assets/2e861acc-9363-4d76-b1c5-5b7594564cc3)

Exit the `ajay` session:
```sh
exit
```
**Sample Output**

![image](https://github.com/user-attachments/assets/21f263f9-155d-4346-a806-247b978e1d78)

---

## **Alternative Method: Add User to `/etc/sudoers.d` Directory**  
Instead of editing the main `sudoers` file, create a new file in `/etc/sudoers.d` for `ajay`.

### **Step 1: Navigate to the sudoers.d Directory**
```sh
cd /etc/sudoers.d
```
**Sample Output**

![image](https://github.com/user-attachments/assets/1bb652d9-fcb4-48a1-82f5-cbe330e0d93b)

### **Step 2: Create a New File for `ajay`**
```sh
vi ajay
```
Add the following line in the file:
```sh
ajay ALL=(ALL) NOPASSWD:/usr/sbin/dmidecode
```
Save and exit (`ESC`, then `:wq`, then `Enter`).

**Sample Output**

![image](https://github.com/user-attachments/assets/9ca3e02e-eef0-4166-886a-2bc2fed99b0e)

### **Step 3: Test the Configuration**
Switch to `ajay`:
```sh
su - ajay
```
Verify sudo permissions:
```sh
sudo -l
```
**Sample Output**

![image](https://github.com/user-attachments/assets/de3c9e46-7bcd-4c61-a07e-840f25034b03)

Run `dmidecode` without a password:
```sh
sudo dmidecode
```
**Sample Output**

![image](https://github.com/user-attachments/assets/9c514d03-2424-4a47-a5e5-04b5566ebe38)

---

## **Conclusion**  
Now, `ajay` can run the `dmidecode` command without a password. This setup ensures security while providing controlled access to necessary commands.
