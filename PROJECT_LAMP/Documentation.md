## WEB STACK IMPLEMENTATION (PROJECT LAMP)
## Overview
The **LAMP stack** is a foundational open-source web development platform. This guide provides a comprehensive walkthrough for deploying and configuring the four essential components on an AWS infrastructure:

*   **L**inux: L 
*   **A**pache: A 
*   **M**ySQL: M
*   **P**HP: P

| Component | Technology | Role |
| :--- | :--- | :--- |
| **Operating System** | Linux | The operating system (Ubuntu Server) |
| **Web Server** | Apache | The HTTP web server |
| **Database** | MySQL | Data Storage |
| **Scripting** | PHP | The server-side scripting language |

---
### 1. EC2 Instance Provisioning
I deployed an **Ubuntu 24.04 LTS (HVM)** virtual server using the `t3.micro` instance type. This was launched within the `us-wast-2` region.

<img width="1899" height="803" alt="Screenshot 2026-02-22 160209" src="https://github.com/user-attachments/assets/5173fb96-6ee0-4a11-944e-ef853a76d00d" />

---
<img width="1554" height="694" alt="Screenshot 2026-02-22 160319" src="https://github.com/user-attachments/assets/79ba768f-6e52-403a-accb-783eecbabdf3" />

### 2. Secure Instance Access
To enable remote management, a custom SSH key pair titled `my-ec2-key` was generated. This allows for secure authentication to the instance via **Port 22**.

### 3. Security Group & Firewall Configuration
The EC2 Security Group was configured as a virtual firewall to regulate inbound traffic. I defined the following rules to ensure the web stack is accessible to the public while maintaining administrative access:


| Protocol | Port | Source | Description |
| :--- | :--- | :--- | :--- |
| **SSH** | 22 | `0.0.0.0/0` | Remote Access (Enabled by default) |
| **HTTP** | 80 | `0.0.0.0/0` | Public Web Traffic |
| **HTTPS** | 443 | `0.0.0.0/0` | Secure Web Traffic (SSL/TLS) |

<img width="1578" height="550" alt="Screenshot 2026-02-22 160420" src="https://github.com/user-attachments/assets/08611b2d-1883-42c7-b5a2-49a3d447cad4" />

---
### 4. Network Infrastructure
For this deployment, the instance was provisioned using the **Default VPC** and **Default Subnet** within the **us-west-2 (Oregon)** region. This configuration ensures the server is assigned a public IPv4 address, allowing it to communicate with the internet and receive inbound web requests.
<img width="1869" height="744" alt="Screenshot 2026-02-22 165721" src="https://github.com/user-attachments/assets/65d5f24d-fe6e-4278-8e38-74fc99a15fed" />
### 5. Establishing SSH Connectivity
To manage the server, I established a secure connection using the **SSH (Secure Shell)** protocol.

**A. Key Permissions**  
First, I navigated to the directory containing the private key (`my key.pem`) and restricted its permissions to ensure it is not publicly viewable. This is a security requirement for SSH clients:
```bash
chmod 400 "my key.pem"
```
Connecting to the Instance
Using the Ubuntu default username and the Public DNS provided by the AWS console, I initialized the remote session with the following command:
```
ssh -i "my key.pem" ubuntu@ec2-35-94-202-24.us-west-2.compute.amazonaws.com

```
<img width="1832" height="682" alt="image" src="https://github.com/user-attachments/assets/3be740f3-ad74-4335-81f0-32b6abed5ad7" />

## Step 1: Installing Apache and Updating the Firewall

## Step 1: Installing Apache and Configuring the Firewall

### 1. Update and Upgrade Package Manager
To ensure the server is running the latest software versions and security patches, I updated the local package index and upgraded all installed packages:

```bash
sudo apt update
sudo apt upgrade -y
```

<img width="785" height="699" alt="Screenshot 2026-02-22 174104" src="https://github.com/user-attachments/assets/085a7dc7-0c69-4233-8492-df0715d70bd8" />
<img width="1280" height="558" alt="Screenshot 2026-02-22 172807" src="https://github.com/user-attachments/assets/dbe6e3a9-a110-4feb-97ec-5ef737e2a52f" />

###2. Enable and verify that apache is running on as a service on the OS.__
```
sudo systemctl enable apache2

```
<img width="871" height="398" alt="Screenshot 2026-02-22 174320" src="https://github.com/user-attachments/assets/2cb9e44b-b886-4b77-b709-fe55bb069033" />

### 3. The server is running and can be accessed locally in the ubuntu shell by running the command below:__

```
curl http://localhost:80
OR
curl http://127.0.0.1:80
```
### 4. Remote Web Server Verification
To ensure the Apache HTTP server is correctly installed and reachable through the AWS security group (firewall), I tested the connection using the instance's public IP address in a web browser.

**Access URL:**
```text
http://35.94.202.24:80

```

<img width="3000" height="2000" alt="apache work" src="https://github.com/user-attachments/assets/e52318b3-d784-4ce0-b825-153e2a7d474d" />

## Step 2: Installing MySQL

### 1. Database Engine Installation
To manage data for our application, I installed **MySQL Server**, a high-performance relational database management system (RDBMS) commonly utilized in PHP environments.

```bash
sudo apt install mysql-server -y

```
<img width="881" height="263" alt="Screenshot 2026-02-22 174823" src="https://github.com/user-attachments/assets/f943c949-1dfe-4e1a-a2e6-668f1b3d77ee" />

### 2. Ensure the database is operational
This ensures the engine is currently running and will automatically restart whenever the system reboots
```
sudo systemctl enable --now mysql
sudo systemctl status mysql
```
<img width="881" height="263" alt="Screenshot 2026-02-22 174823" src="https://github.com/user-attachments/assets/c655b595-c9a9-4935-926c-7b0a717ab320" />



I used systemctl to enable and start the MySQL service simultaneously. 

### 2. Ensure the database is operational
```
sudo mysql
```
This connects to the MySQL server as the administrative database user root infered by the use of sudo when running the command.

### 3. Set a password for root user using mysql_native_password as default authentication method.

Here, the user's password was defined as "Password1"
```
ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'Password1';
```
Ithen had to exit the shell using 
```
exit
```
## Step 3: Installing PHP
To enable dynamic web processing, I installed the PHP package along with essential dependencies. This included php-mysql to allow communication between PHP and the database, and libapache2-mod-php, which integrates PHP with the Apache web server to handle script execution.
```
sudo apt install php libapache2-mod-php php-mysql -y
```
<img width="938" height="354" alt="Screenshot 2026-02-22 175218" src="https://github.com/user-attachments/assets/d45daf18-1d5a-447b-ae60-9b22f66ebad3" />

Confirm the PHP version
```
php -v
```
To test the environment effectively, I configured a dedicated Apache Virtual Host to manage the website files. Utilizing Virtual Hosts allows a single server to host multiple independent websites simultaneously, providing a professional structure that remains seamless and invisible to the end users. 

## Step 3: Configure Apache Virtual Host (Apache)
Prepare Document Root
Create the directory for projectlamp and assign ownership to the current user:
```
sudo mkdir /var/www/projectlamp
sudo chown -R $USER:$USER /var/www/projectlamp
```
### Create Virtual Host Configuration
Open a new configuration file:
```
sudo vi /etc/apache2/sites-available/projectlamp.conf
```
Paste the following configuration:
```
<VirtualHost *:80>
  ServerName projectlamp
  DocumentRoot /var/www/projectlamp
  ErrorLog ${APACHE_LOG_DIR}/error.log
  CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
```
### Enable Configuration and Reload Apache
Enable the new site, disable the default site, and reload to apply changes:
```
sudo a2ensite projectlamp
sudo a2dissite 000-default
sudo apache2ctl configtest
sudo systemctl reload apache2
```
### Test the Deployment
Create a landing page to verify the virtual host is active:
```
echo "Hello LAMP from $(hostname)" > /var/www/projectlamp/index.html
```
Access your site via http://<Public-IP>:80.

## Enable PHP on the Website

### 1. Adjust Directory Index Priority
To prioritize index.php over index.html, edit the Apache Directory Index configuration:
```
sudo vi /etc/apache2/mods-enabled/dir.conf
```
Update the order as follows:
```
DirectoryIndex index.php index.html index.cgi index.pl index.xhtml index.htm
```
### 2. Verify PHP Processing
Reload Apache and create a test script:
```
sudo systemctl reload apache2
echo "<?php phpinfo();" > /var/www/projectlamp/index.php
```
Refresh your browser. Once verified, remove the file for security:
```
sudo rm /var/www/projectlamp/index.php
```

<img width="1512" height="907" alt="php 111111" src="https://github.com/user-attachments/assets/d99886cd-563e-4de2-a4ba-bbbc6328cd0d" />

## Conclusion
The LAMP stack is now fully configured. This environment is ready for deploying scalable PHP applications on an Apache web server.







