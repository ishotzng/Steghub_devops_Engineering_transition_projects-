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
## Step 2: Installing MySQL

### 1. Database Engine Installation
To manage data for our application, I installed **MySQL Server**, a high-performance relational database management system (RDBMS) commonly utilized in PHP environments.

```bash
sudo apt install mysql-server -y


