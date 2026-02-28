# WEB STACK IMPLEMENTATION (LEMP STACK) ON AWS

## Introduction

The LEMP stack is an open source web development platform that combines Linux, Nginx, MySQL, and PHP. This documentation explains the setup, configuration, and verification of the LEMP architecture on an AWS EC2 instance running Ubuntu Server 24.04 LTS.

---

# Step 0. Prerequisites

### 1. Launch the EC2 Instance

An EC2 instance (t2.micro) running Ubuntu 24.04 LTS was launched in the us-east-1 region.


<img width="1899" height="803" alt="Screenshot 2026-02-22 160209" src="https://github.com/user-attachments/assets/d3c1bc4d-8696-429a-a575-8ad22690b695" />
<img width="1616" height="658" alt="Screenshot 2026-02-28 144244" src="https://github.com/user-attachments/assets/a81961a5-396e-4832-ba9c-ae93d06c2545" />


### 2. Create SSH Key Pair

A key pair named **my key.pem** was created for SSH access.
<img width="922" height="824" alt="Screenshot 2026-02-28 130755" src="https://github.com/user-attachments/assets/dff66eb5-f94b-471b-be9b-a4a0df75f1f9" />



### 3. Security Group Configuration

Inbound rules included:

- HTTP (80) from anywhere  
- HTTPS (443) from anywhere  
- SSH (22) from anywhere  

<img width="1625" height="281" alt="Screenshot 2026-02-28 144203" src="https://github.com/user-attachments/assets/9e77a12f-f949-42a0-a7b1-e357dbab6d7c" />


### 4. Networking

Default VPC and subnet were used.



### 5. Connect to EC2 Instance

Change permission on private key and connect:

```bash
chmod 400 "my key.pem"
```

```bash
ssh -i "my key.pem" ubuntu@34.222.49.97
```

![SSH Access](./images/ssh-access.png)

---

# Step 1. Install Nginx Web Server

### 1. Update and Upgrade System

```bash
sudo apt update
sudo apt upgrade -y
```

![Update](./images/update-ec2.png)  
![Upgrade](./images/upgrade-ec2.png)

### 2. Install Nginx

```bash
sudo apt install nginx -y
```

![Install Nginx](./images/install-nginx.png)

### 3. Verify Nginx is Running

```bash
sudo systemctl status nginx
```

![Status](./images/nginx-status.png)

### 4. Test Local Access

```bash
curl http://127.0.0.1
```

![Local Curl](./images/curl-access.png)

### 5. Test Public Access

Open in browser:

```
http://34.222.49.97
```

![Default Page](./images/nginx-default.png)

### 6. Retrieve Public IP via Metadata

```bash
curl -s http://169.254.169.254/latest/meta-data/public-ipv4
```

If 401 Unauthorized appears:

![401](./images/curl-401-unauthorized.png)

Set metadata options to Optional.

![IMDS](./images/imds-setting.png)

Run again:

```bash
curl -s http://169.254.169.254/latest/meta-data/public-ipv4
```

![Public IP](./images/curl-authorized.png)

---

# Step 2. Install MySQL

### 1. Install MySQL Server

```bash
sudo apt install mysql-server -y
```

![Install MySQL](./images/install-mysql.png)

### 2. Log Into MySQL Shell

```bash
sudo mysql
```

![MySQL Shell](./images/mysql-shell.png)

### 3. Set Root Password

```sql
ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'Admin123$';
```

![Root Password](./images/root-password.png)

Exit:

```bash
exit
```

### 4. Secure MySQL

```bash
sudo mysql_secure_installation
```

![Secure MySQL](./images/secure-mysql.png)

### 5. Log In With Password

```bash
sudo mysql -p
```

![Login](./images/mysql-console.png)

Exit:

```bash
exit
```

---

# Step 3. Install PHP

Install PHP FastCGI and MySQL module:

```bash
sudo apt install php-fpm php-mysql -y
```

![Install PHP](./images/install-php.png)

---

# Step 4. Configure Nginx to Use PHP Processor

### 1. Create Web Root Directory

```bash
sudo mkdir /var/www/projectLEMP
```

![Directory](./images/mkdir-root-dir.png)

### 2. Assign Ownership

```bash
sudo chown -R $USER:$USER /var/www/projectLEMP
```

![Change Owner](./images/change-owner.png)

### 3. Create Nginx Server Block

```bash
sudo nano /etc/nginx/sites-available/projectLEMP
```

Paste:

```nginx
server {
    listen 80;
    server_name projectLEMP www.projectLEMP;
    root /var/www/projectLEMP;

    index index.html index.htm index.php;

    location / {
        try_files $uri $uri/ =404;
    }

    location ~ \.php$ {
        include snippets/fastcgi-php.conf;
        fastcgi_pass unix:/var/run/php/php8.3-fpm.sock;
    }

    location ~ /\.ht {
        deny all;
    }
}
```

![Nginx Config](./images/nginx-config.png)

### 4. Enable Configuration

```bash
sudo ln -s /etc/nginx/sites-available/projectLEMP /etc/nginx/sites-enabled/
```

![Symlink](./images/sys-link.png)

### 5. Test Nginx Syntax

```bash
sudo nginx -t
```

![Syntax Test](./images/test-syntax.png)

### 6. Disable Default Host

```bash
sudo unlink /etc/nginx/sites-enabled/default
```

![Disable Default](./images/disable-default-nginx.png)

### 7. Reload Nginx

```bash
sudo systemctl reload nginx
```

![Reload](./images/reload-nginx.png)

### 8. Create Test index.html

```bash
sudo bash -c 'echo "Hello LEMP from hostname $(curl -s http://169.254.169.254/latest/meta-data/public-hostname) with public IP $(curl -s http://169.254.169.254/latest/meta-data/public-ipv4)" > /var/www/projectLEMP/index.html'
```

![Site Content](./images/site-content.png)

Open:

```
http://34.222.49.97
```

![Site IP](./images/site-IP.png)

---

# Step 5. Test PHP with Nginx

### 1. Create phpinfo File

```bash
sudo nano /var/www/projectLEMP/info.php
```

Paste:

```php
<?php
phpinfo();
```

### 2. Check via Browser

```
http://34.222.49.97/info.php
```

![PHP Info](./images/php-page.png)

Remove file:

```bash
sudo rm /var/www/projectLEMP/info.php
```

---

# Step 6. Retrieve Data from MySQL Using PHP

### 1. Connect to MySQL

```bash
sudo mysql -p
```

### 2. Create Database

```sql
CREATE DATABASE todo_database;
```

![Create DB](./images/create-db.png)

### 3. Create User and Grant Access

```sql
CREATE USER 'todo_user'@'%' IDENTIFIED WITH mysql_native_password BY 'Admin123$';
GRANT ALL ON todo_database.* TO 'todo_user'@'%';
```

![Create User](./images/create-user-and-privilege.png)

Exit:

```bash
exit
```

### 4. Confirm Access

```bash
mysql -u todo_user -p
SHOW DATABASES;
```

![Show DB](./images/show-db.png)

### 5. Create Table

```sql
CREATE TABLE todo_database.todo_list (
    item_id INT AUTO_INCREMENT,
    content VARCHAR(255),
    PRIMARY KEY(item_id)
);
```

### 6. Insert Rows

```sql
INSERT INTO todo_database.todo_list (content) VALUES ("My first important item");
INSERT INTO todo_database.todo_list (content) VALUES ("My second important item");
INSERT INTO todo_database.todo_list (content) VALUES ("My third important item");
INSERT INTO todo_database.todo_list (content) VALUES ("and this one more thing");
```

![Insert Rows](./images/insert-rows.png)

### 7. Verify Data

```sql
SELECT * FROM todo_database.todo_list;
```

![Query](./images/query-table.png)

Exit:

```bash
exit
```

---

# Create PHP Script to Query the Database

### 1. Create Script

```bash
sudo nano /var/www/projectLEMP/todo_list.php
```

Paste:

```php
<?php
$user = "todo_user";
$password = "Admin123$";
$database = "todo_database";
$table = "todo_list";

try {
    $db = new PDO("mysql:host=localhost;dbname=$database", $user, $password);
    echo "<h2>TODO</h2><ol>";
    foreach ($db->query("SELECT content FROM $table") as $row) {
        echo "<li>" . $row['content'] . "</li>";
    }
    echo "</ol>";
} catch (PDOException $e) {
    print "Error!: " . $e->getMessage() . "<br/>";
    die();
}
?>
```

![PHP Script](./images/php-script.png)

### 2. Access Script in Browser

```
http://34.222.49.97/todo_list.php
```

If 502 error appears, update Nginx for php8.3:

![Update PHP](./images/php-v-update.png)

Reload:

```bash
sudo systemctl reload nginx
```

Open again:

```
http://34.222.49.97/todo_list.php
```

![Result](./images/php-site-ip.png)

---

# Conclusion

The LEMP stack is now fully installed and operational. You can proceed to deploy applications, configure virtual hosts, or introduce additional services such as SSL, firewalls, or CI pipelines.
