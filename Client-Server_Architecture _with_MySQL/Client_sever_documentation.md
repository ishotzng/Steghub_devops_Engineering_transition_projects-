# Implementing a Client-Server Architecture with MySQL DBMS

---

The **Client-Server** architecture is a fundamental network model in which two machines
communicate over a network. One requesting resources or services (the client),
and the other providing them (the server). This guide walks through a complete
deployment of a **MySQL**-based client-server setup on **AWS** infrastructure,
where two EC2 instances are configured to communicate securely over a private network.

| Component | Technology | Role |
| :--- | :--- | :--- |
| **Server Instance** | [AWS EC2](https://aws.amazon.com/ec2) (Ubuntu 24.04) | Hosts the MySQL Server database engine |
| **Client Instance** | [AWS EC2](https://aws.amazon.com/ec2) (Ubuntu 24.04) | Runs MySQL Client to query the remote server |
| **Database Engine** | [MySQL Server](https://www.mysql.com) | Stores, manages, and serves relational data |
| **Client Utility** | [MySQL Client](https://www.mysql.com) | Issues SQL queries to the remote database |
| **Network Layer** | AWS VPC (Private IP) | Enables secure internal communication on port 3306 |
---

## Infrastructure Summary

| Role | Public IPv4 | Private IPv4 |
|------|------------|--------------|
| MySQL Server | `44.252.66.59` | `172.31.15.203` |
| MySQL Client | `35.89.67.5` | `172.31.15.174` |

- **Instance type:** t2.micro  
- **OS:** Ubuntu 24.04 LTS (HVM)  
- **Region:** us-east-1  
- **SSH Key:** `my key.pem`

---

## Step 1 — Launch and Configure Two EC2 Instances

Two EC2 instances were provisioned via the AWS Console — one designated as the **mysql server** and the other as the **mysql client**.


Both instances were initially configured with the following info
<img width="1628" height="745" alt="Screenshot 2026-04-18 070648" src="https://github.com/user-attachments/assets/b231bc22-9d72-44fb-beda-75d1bd0b6972" />
---
<img width="1635" height="730" alt="Screenshot 2026-04-18 070621" src="https://github.com/user-attachments/assets/ad77ec9c-5098-411f-917c-278525a08b90" />
und security rule:

| Type | Protocol | Port | Source |
|------|----------|------|--------|
| SSH | TCP | 22 | 0.0.0.0/0 |



The SSH key `my key.pem` was attached to both instances to allow terminal access.

---

## Step 2 — Install MySQL Server on the Server Instance

### 2.1 — Connect to the MySQL Server

Set the correct permissions on the key file and SSH into the server:

```bash
chmod 400 "my key.pem"
ssh -i "my key.pem" ubuntu@44.252.66.59
```
<img width="932" height="450" alt="Screenshot 2026-04-18 070822" src="https://github.com/user-attachments/assets/6f22431e-4071-4869-a74d-600a423201b7" />
---
<img width="898" height="756" alt="Screenshot 2026-04-18 070852" src="https://github.com/user-attachments/assets/ffcc94ce-314a-4935-96f5-3e0d1dbc30a8" />

### 2.2 — Update and Upgrade the System

```bash
sudo apt update && sudo apt upgrade -y
```
<img width="930" height="596" alt="Screenshot 2026-04-18 070936" src="https://github.com/user-attachments/assets/546c8ae1-1b92-4344-9b0c-696d37ec6f58" />


### 2.3 — Install MySQL Server

```bash
sudo apt install mysql-server -y
```
<img width="885" height="275" alt="Screenshot 2026-04-18 071235" src="https://github.com/user-attachments/assets/7438d962-092e-45d6-81b5-aed50acb11c6" />


### 2.4 — Enable MySQL to Start on Boot

```bash
sudo systemctl enable mysql
```
<img width="931" height="442" alt="Screenshot 2026-04-18 071257" src="https://github.com/user-attachments/assets/22bf659e-76e1-41d6-9f40-4655fd68afaf" />

---

## Step 3 — Install MySQL Client on the Client Instance

### 3.1 — Connect to the MySQL Client

Open a new terminal and SSH into the client instance:

```bash
ssh -i "my key.pem" ubuntu@35.89.67.5
```
<img width="932" height="691" alt="Screenshot 2026-04-18 071436" src="https://github.com/user-attachments/assets/1ede4fa7-6d1e-4099-945c-8b98e9ddedd2" />


### 3.2 — Update and Upgrade the System

```bash
sudo apt update && sudo apt upgrade -y
```

<img width="922" height="593" alt="Screenshot 2026-04-18 071508" src="https://github.com/user-attachments/assets/5cc53dbb-3343-4e3d-803c-813e89b78f4e" />

---

### 3.3 — Install MySQL Client

```bash
sudo apt install mysql-client -y
```

---

## Step 4 — Configure the Server's Security Group for Remote MySQL Access

By default, both EC2 instances reside in the same AWS Virtual Private Cloud (VPC) and can communicate via their **private IP addresses**. MySQL listens on **TCP port 3306** by default.

Update the **mysql server's** inbound security group rules to allow traffic on port 3306 exclusively from the client's private IP:

| Type | Protocol | Port | Source |
|------|----------|------|--------|
| SSH | TCP | 22 | 0.0.0.0/0 |
| Custom TCP | TCP | 3306 | 172.31.15.174/32 |

> Restricting access to the client's private IP (`172.31.15.174/32`) rather than `0.0.0.0/0` is a security best practice — it ensures only your client instance can reach the database port.
>
<img width="1534" height="249" alt="Screenshot 2026-04-18 073547" src="https://github.com/user-attachments/assets/32f5ec53-bac0-44f2-aea1-61c127a4054c" />


---

## Step 5 — Harden and Configure MySQL Server

Switch back to the **server terminal** for the following steps.

### 5.1 — Run the MySQL Secure Installation Script

```bash
sudo mysql_secure_installation
```
<img width="908" height="412" alt="Screenshot 2026-04-18 073943" src="https://github.com/user-attachments/assets/5af1a5ff-d7b5-4538-86a3-326ee763ac99" />


---

Follow the prompts to set a root password, remove anonymous users, disallow remote root login, and remove the test database.

### 5.2 — Access the MySQL Shell

```bash
sudo mysql
```
<img width="934" height="314" alt="Screenshot 2026-04-18 074735" src="https://github.com/user-attachments/assets/18a4ae9c-a0ab-4bc9-b8da-be192b7bfd37" />

---

### 5.3 — Create a Remote User, Database, and Grant Privileges

Inside the MySQL shell, run the following commands:

```sql
CREATE USER 'client'@'%' IDENTIFIED WITH mysql_native_password BY 'User123$';

CREATE DATABASE test_db;

GRANT ALL ON test_db.* TO 'client'@'%' WITH GRANT OPTION;

FLUSH PRIVILEGES;
```
<img width="941" height="386" alt="Screenshot 2026-04-18 074712" src="https://github.com/user-attachments/assets/f1525886-f2b9-4ad4-adbf-f1d5eff2c5c8" />

---
### 5.4 — Allow Remote Connections by Editing the MySQL Config

Open the MySQL configuration file:

```bash
sudo vim /etc/mysql/mysql.conf.d/mysqld.cnf
```
<img width="901" height="36" alt="Screenshot 2026-04-18 075039" src="https://github.com/user-attachments/assets/9b161870-6647-4046-a63f-e1e1c735a22e" />

---

Locate the `bind-address` directive and update it as follows:

```ini
# Before
bind-address = 127.0.0.1

# After
bind-address = 0.0.0.0
```
<img width="902" height="655" alt="Screenshot 2026-04-18 075016" src="https://github.com/user-attachments/assets/a2392d09-8231-4942-9eb4-16f306c6e536" />

---

Save the file and restart MySQL to apply the change:

```bash
sudo systemctl restart mysql
```

---

## Step 6 — Connect from the Client to the Remote MySQL Server

Switch to the **client terminal**. Use the `mysql` utility to connect to the server's **private IP address** — no SSH tunnelling needed:

```bash
mysql -u client -h 172.31.15.203 -p
```
<img width="933" height="409" alt="Screenshot 2026-04-18 080733" src="https://github.com/user-attachments/assets/de7e90c4-9135-49e3-aa0f-a2cc331655db" />

---
Enter the password `User123$` when prompted. A successful connection will drop you into the MySQL prompt.
---
<img width="933" height="409" alt="Screenshot 2026-04-18 080733" src="https://github.com/user-attachments/assets/b65d5e3c-927b-4bcb-9349-35405b774e1d" />

---

## Step 7 — Verify the Connection and Run SQL Queries

### 7.1 — List Available Databases

```sql
SHOW DATABASES;
```

You should see `test_db` in the output, confirming the remote connection is working.

### 7.2 — Create a Table and Insert Records

```sql
CREATE TABLE test_db.test_table (
  item_id INT AUTO_INCREMENT,
  content VARCHAR(255),
  PRIMARY KEY(item_id)
);

INSERT INTO test_db.test_table (content) VALUES ('My first choice football club is Chelsea');
INSERT INTO test_db.test_table (content) VALUES ('My second choice football club is R.Madrid');
```
<img width="942" height="594" alt="Screenshot 2026-04-18 080812" src="https://github.com/user-attachments/assets/3a902c7f-fd97-4fa8-a80f-37bcabde8cd8" />

---

### 7.3 — Query the Table

```sql
SELECT * FROM test_db.test_table;
```
<img width="942" height="594" alt="Screenshot 2026-04-18 080812" src="https://github.com/user-attachments/assets/1923bd84-32bd-490c-a4af-4799b27035e9" />

----
Expected output:

```
+---------+-------------------------------------------+
| item_id | content                                   |
+---------+-------------------------------------------+
|       1 | My first choice football club is Chelsea  |
|       2 | My second choice football club is R.Madrid|
+---------+-------------------------------------------+
2 rows in set (0.00 sec)
```
<img width="913" height="681" alt="Screenshot 2026-04-18 080901" src="https://github.com/user-attachments/assets/d85fc70e-6bf2-42a9-944a-f078e28a691e" />


---

At this point the project is complete. You have successfully:

- Provisioned two EC2 Ubuntu instances on AWS (server + client)
- Installed MySQL Server on one and MySQL Client on the other
- Configured security groups to allow MySQL traffic only from the client's private IP
- Created a dedicated MySQL user and database
- Configured MySQL to accept remote connections
- Verified the end-to-end connection by executing SQL queries from the client — entirely without SSH

## Project Outcome
The deployment is complete and fully operational. Two Ubuntu EC2 instances now function as dedicated database server and client, communicating securely over AWS's internal network on port 3306. The configuration reflects Production - Conscious decisions — Least-privilege security group rules, a dedicated database user with scoped permissions, and a remotely accessible MySQL instance hardened with the secure installation script. The architecture is ready to serve as a backend for any application layer.
This constitutes a fully functional **MySQL Client-Server architecture** deployed on AWS.
