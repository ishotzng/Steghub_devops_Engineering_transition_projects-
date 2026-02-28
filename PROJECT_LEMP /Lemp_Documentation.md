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

<img width="922" height="824" alt="Screenshot 2026-02-28 130755" src="https://github.com/user-attachments/assets/b1c075f1-ad03-4922-bb22-baba145fa15d" />


---

# Step 1. Install Nginx Web Server

### 1. Update and Upgrade System

```bash
sudo apt update
sudo apt upgrade -y
```
<img width="1138" height="945" alt="Screenshot 2026-02-28 131401" src="https://github.com/user-attachments/assets/10edeb84-3d2e-4f3c-b526-4e62373e5f54" />
<img width="1164" height="570" alt="Screenshot 2026-02-28 131420" src="https://github.com/user-attachments/assets/7f5410db-29f9-46d6-bcb2-7121057e297c" />



### 2. Install Nginx

```bash
sudo apt install nginx -y
```

<img width="678" height="327" alt="Screenshot 2026-02-28 131502" src="https://github.com/user-attachments/assets/f4200830-8cef-4fc3-a2e3-f4583f7e1d60" />
<img width="893" height="860" alt="Screenshot 2026-02-28 131527" src="https://github.com/user-attachments/assets/10605007-d03d-4445-9c09-b90e1f574aae" />



### 3. Verify Nginx is Running

```bash
sudo systemctl status nginx
```

<img width="933" height="398" alt="Screenshot 2026-02-28 131637" src="https://github.com/user-attachments/assets/01fa96db-17df-49b4-814e-0f35b00b2000" />


### 4. Test Local Access

```bash
curl http://127.0.0.1
```

<img width="659" height="526" alt="Screenshot 2026-02-28 133114" src="https://github.com/user-attachments/assets/74cffd12-0d72-433f-a2cd-6bf7bd38765d" />


### 5. Test Public Access

Open in browser:

```
http://34.222.49.97
```

<img width="949" height="526" alt="Screenshot 2026-02-28 133339" src="https://github.com/user-attachments/assets/ab43a856-23e4-4477-9292-77753811498b" />


---

# Step 2. Install MySQL

### 1. Install MySQL Server

```bash
sudo apt install mysql-server -y
```

<img width="740" height="584" alt="Screenshot 2026-02-28 134036" src="https://github.com/user-attachments/assets/3fa009d3-f7f7-4ddf-92f8-903df28b4611" />

<img width="1029" height="1021" alt="Screenshot 2026-02-28 134138" src="https://github.com/user-attachments/assets/8a80ca2e-9f6a-4be5-b0a7-4424c644c17e" />



### 2. Log Into MySQL Shell

```bash
sudo mysql
```

<img width="714" height="354" alt="Screenshot 2026-02-28 134229" src="https://github.com/user-attachments/assets/f217be87-8cf5-4e76-8aaf-5b2e95353b1d" />


### 3. Set Root Password

```sql
ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'Admin123$';
```

<img width="765" height="983" alt="Screenshot 2026-02-28 134945" src="https://github.com/user-attachments/assets/bd05cda5-156c-428b-a49d-1c0e39360105" />
<img width="943" height="793" alt="Screenshot 2026-02-28 134534" src="https://github.com/user-attachments/assets/ede60142-739c-4484-8a61-7650003f2c8b" />


Exit:

```bash
exit
```

### 4. Secure MySQL

```bash
sudo mysql_secure_installation
```

<img width="1182" height="1014" alt="Screenshot 2026-02-28 135534" src="https://github.com/user-attachments/assets/815c39e9-f964-46b9-ab49-3687ebf96cd2" />


### 5. Log In With Password

```bash
sudo mysql -p
```

<img width="1598" height="1016" alt="Screenshot 2026-02-28 135631" src="https://github.com/user-attachments/assets/9297251f-01f4-4f57-acb5-d670c237cace" />


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

<img width="1121" height="1014" alt="Screenshot 2026-02-28 140029" src="https://github.com/user-attachments/assets/8b847ee5-e245-4b30-b989-4686d285c99a" />
<img width="1186" height="1024" alt="Screenshot 2026-02-28 140009" src="https://github.com/user-attachments/assets/06894066-c3be-490d-b620-58b95403f38b" />


---

# Step 4. Configure Nginx to Use PHP Processor

### 1. Create Web Root Directory

```bash
sudo mkdir /var/www/projectLEMP
```

<img width="727" height="509" alt="Screenshot 2026-02-28 140243" src="https://github.com/user-attachments/assets/b4d88f88-c900-4e87-b1e2-b25e67a4d372" />


### 2. Assign Ownership

```bash
sudo chown -R $USER:$USER /var/www/projectLEMP
```



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


### 4. Enable Configuration

```bash
sudo ln -s /etc/nginx/sites-available/projectLEMP /etc/nginx/sites-enabled/
```

<img width="856" height="396" alt="Screenshot 2026-02-28 140546" src="https://github.com/user-attachments/assets/f23712e5-a419-4fb1-87f9-68eeec0b0322" />


### 5. Test Nginx Syntax

```bash
sudo nginx -t
```

<img width="734" height="267" alt="Screenshot 2026-02-28 140608" src="https://github.com/user-attachments/assets/7f13aa39-f8be-4798-8b2a-fd7160103003" />


### 6. Disable Default Host

```bash
sudo unlink /etc/nginx/sites-enabled/default
```



### 7. Reload Nginx

```bash
sudo systemctl reload nginx
```



### 8. Create Test index.html

```bash
sudo bash -c 'echo "Hello LEMP from hostname $(curl -s http://169.254.169.254/latest/meta-data/public-hostname) with public IP $(curl -s http://169.254.169.254/latest/meta-data/public-ipv4)" > /var/www/projectLEMP/index.html'
```

<img width="1530" height="228" alt="Screenshot 2026-02-28 140943" src="https://github.com/user-attachments/assets/7e62e252-599b-49a4-b5df-b3f94a201a8a" />


Open:

```
http://34.222.49.97
```

<img width="958" height="749" alt="Screenshot 2026-02-28 141016" src="https://github.com/user-attachments/assets/191ae144-9d9b-48ac-b853-9371e57f4607" />


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
<img width="1853" height="946" alt="Screenshot 2026-02-28 141611" src="https://github.com/user-attachments/assets/15c600df-4007-4e47-8440-9aa1f0db3479" />



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

<img width="900" height="674" alt="Screenshot 2026-02-28 142835" src="https://github.com/user-attachments/assets/60656f58-d9bf-4ee6-8283-16fccdc07694" />


### 3. Create User and Grant Access

```sql
CREATE USER 'todo_user'@'%' IDENTIFIED WITH mysql_native_password BY 'Admin123$';
GRANT ALL ON todo_database.* TO 'todo_user'@'%';
```

<img width="900" height="674" alt="Screenshot 2026-02-28 142835" src="https://github.com/user-attachments/assets/82668344-98a0-40ab-98bc-253bbcb1c20a" />


Exit:

```bash
exit
```

### 4. Confirm Access

```bash
mysql -u todo_user -p
SHOW DATABASES;
```

<img width="817" height="843" alt="Screenshot 2026-02-28 143210" src="https://github.com/user-attachments/assets/3d60cb10-b6d2-4f3c-a9da-6a2a49fbaeef" />


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



### 7. Verify Data

```sql
SELECT * FROM todo_database.todo_list;
```

<img width="817" height="843" alt="Screenshot 2026-02-28 143210" src="https://github.com/user-attachments/assets/306ab10a-c647-40a9-81e2-62c6c71d4a53" />


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

<img width="820" height="502" alt="Screenshot 2026-02-28 143354" src="https://github.com/user-attachments/assets/6a2fe1a6-33a0-447b-ac89-c4db17df5bf7" />


### 2. Access Script in Browser

```

http://34.222.49.97/todo_list.php
```

<img width="1229" height="906" alt="Screenshot 2026-02-28 143430" src="https://github.com/user-attachments/assets/902cf36b-f2b3-4233-8161-921004b8cbf3" />


---

# Conclusion

Deploy reliable and scalable web solutions using the LEMP stack. By integrating Linux, Nginx, MySQL/MariaDB, and PHP, developers gain a powerful framework capable of handling modern web traffic with ease.
