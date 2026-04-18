# Implementing a Client-Server Architecture with MySQL DBMS

---

## Overview

This project demonstrates how to set up a MySQL client-server architecture using two Linux-based Ubuntu servers hosted on AWS EC2. The **mysql server** hosts the database engine, while the **mysql client** connects to it remotely over the local network — no SSH required for the database connection itself.

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


Both instances were initially configured with the following inbound security rule:

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

### 2.2 — Update and Upgrade the System

```bash
sudo apt update && sudo apt upgrade -y
```

### 2.3 — Install MySQL Server

```bash
sudo apt install mysql-server -y
```

### 2.4 — Enable MySQL to Start on Boot

```bash
sudo systemctl enable mysql
```

---

## Step 3 — Install MySQL Client on the Client Instance

### 3.1 — Connect to the MySQL Client

Open a new terminal and SSH into the client instance:

```bash
ssh -i "my key.pem" ubuntu@35.89.67.5
```

### 3.2 — Update and Upgrade the System

```bash
sudo apt update && sudo apt upgrade -y
```

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

---

## Step 5 — Harden and Configure MySQL Server

Switch back to the **server terminal** for the following steps.

### 5.1 — Run the MySQL Secure Installation Script

```bash
sudo mysql_secure_installation
```

Follow the prompts to set a root password, remove anonymous users, disallow remote root login, and remove the test database.

### 5.2 — Access the MySQL Shell

```bash
sudo mysql
```

### 5.3 — Create a Remote User, Database, and Grant Privileges

Inside the MySQL shell, run the following commands:

```sql
CREATE USER 'client'@'%' IDENTIFIED WITH mysql_native_password BY 'User123$';

CREATE DATABASE test_db;

GRANT ALL ON test_db.* TO 'client'@'%' WITH GRANT OPTION;

FLUSH PRIVILEGES;
```

### 5.4 — Allow Remote Connections by Editing the MySQL Config

Open the MySQL configuration file:

```bash
sudo vim /etc/mysql/mysql.conf.d/mysqld.cnf
```

Locate the `bind-address` directive and update it as follows:

```ini
# Before
bind-address = 127.0.0.1

# After
bind-address = 0.0.0.0
```

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

Enter the password `User123$` when prompted. A successful connection will drop you into the MySQL prompt.

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

### 7.3 — Query the Table

```sql
SELECT * FROM test_db.test_table;
```

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

---

## Summary

At this point the project is complete. You have successfully:

- Provisioned two EC2 Ubuntu instances on AWS (server + client)
- Installed MySQL Server on one and MySQL Client on the other
- Configured security groups to allow MySQL traffic only from the client's private IP
- Created a dedicated MySQL user and database
- Configured MySQL to accept remote connections
- Verified the end-to-end connection by executing SQL queries from the client — entirely without SSH

This constitutes a fully functional **MySQL Client-Server architecture** deployed on AWS.
