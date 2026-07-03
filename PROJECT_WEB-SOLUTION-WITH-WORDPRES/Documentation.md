# WEB SOLUTION WITH WORDPRESS ON AWS

## Introduction

This project demonstrates how to deploy a **WordPress-based web solution** on **Amazon Web Services (AWS)** using two **Red Hat Enterprise Linux (RHEL) 10.2** EC2 instances. The infrastructure follows a two-tier architecture where the web server hosts the WordPress application, while a dedicated database server runs MySQL 8.4.

To improve storage management and scalability, **Logical Volume Manager (LVM)** is configured using three **10 GiB Amazon EBS volumes** on each server. This setup separates application data, database files, and system logs into dedicated logical volumes, making storage easier to manage and extend.

The deployment consists of the following core components:

- **Apache HTTP Server** – Hosts the WordPress website.
- **PHP** – Executes WordPress application code.
- **WordPress** – Content Management System (CMS).
- **MySQL 8.4** – Relational database used by WordPress.
- **LVM (Logical Volume Manager)** – Manages storage across multiple EBS volumes.

| Component | Technology | Role |
| :--- | :--- | :--- |
| **Operating System** | Red Hat Enterprise Linux 10.2 | Hosts the application and database servers |
| **Web Server** | Apache HTTP Server | Serves the WordPress application |
| **Programming Language** | PHP | Processes WordPress application logic |
| **Content Management System** | WordPress | Website and content management platform |
| **Database Server** | MySQL 8.4 | Stores WordPress data |
| **Storage Management** | LVM (Logical Volume Manager) | Manages logical storage volumes across EBS disks |
| **Cloud Platform** | Amazon EC2 | Virtual servers hosting the solution |
| **Persistent Storage** | Amazon EBS | Block storage attached to EC2 instances |

## Project Architecture

The solution consists of two dedicated EC2 instances connected through a private network:

### Web Server

- Apache HTTP Server
- PHP
- WordPress
- LVM configured with:
  - `apps-lv`
  - `logs-lv`

### Database Server

- MySQL 8.4
- LVM configured with:
  - `db-lv`
  - `logs-lv`

### Networking

- Web Server Private IP: **172.31.44.246**
- Database Server Private IP: **172.31.42.120**
- WordPress communicates with MySQL over **TCP Port 3306**

### Public Access

- Web Server Public IP: **44.245.157.133**

------------------------------------------------------------------------

# Step 1 --- Prepare Web Server

Launch a Red Hat EC2 instance and attach **three 10 GiB EBS volumes**.

<img width="1612" height="687" alt="Screenshot 2026-07-03 172916" src="https://github.com/user-attachments/assets/1dbbfdeb-8c4a-404e-9301-fa649d1a4f07" />
<img width="1551" height="614" alt="Screenshot 2026-07-03 172753" src="https://github.com/user-attachments/assets/8d0a32ec-d7c0-4353-996b-0a34b7ee2018" />
<img width="1181" height="284" alt="Screenshot 2026-07-03 173145" src="https://github.com/user-attachments/assets/81f301bd-eebd-437f-b0fc-426b260af0d6" />
<img width="1178" height="268" alt="Screenshot 2026-07-03 173100" src="https://github.com/user-attachments/assets/89ec2b32-dee5-4a0c-9d37-8785e198edf5" />
<img width="1703" height="199" alt="Screenshot 2026-07-03 173037" src="https://github.com/user-attachments/assets/599339f3-6dfc-4969-9b6c-a3830fef08ca" />



## Verify attached disks

``` bash
lsblk
df -h
ls /dev
```
<img width="893" height="486" alt="Screenshot 2026-05-26 165059" src="https://github.com/user-attachments/assets/f8cdacb6-7804-4b0d-b736-a9a9902be418" />
<img width="932" height="591" alt="Screenshot 2026-07-03 173319" src="https://github.com/user-attachments/assets/d5179a32-19c6-4540-8ee6-85a548dff667" />


## Partition disks

Because this project used **RHEL 10**, `gdisk` was unavailable. `fdisk`
was used instead.

Repeat for `nvme1n1`, `nvme2n1`, and `nvme3n1`:

``` bash
sudo fdisk /dev/nvme1n1
```

Inside `fdisk`:

``` text
g
n
t
lvm
w
```
<img width="919" height="689" alt="Screenshot 2026-05-26 172234" src="https://github.com/user-attachments/assets/cac97c84-5ae3-493c-a5d7-b02cbecf8037" />

## Configure LVM

``` bash
sudo dnf install lvm2 -y
sudo pvcreate /dev/nvme1n1p1 /dev/nvme2n1p1 /dev/nvme3n1p1
sudo vgcreate webdata-vg /dev/nvme1n1p1 /dev/nvme2n1p1 /dev/nvme3n1p1
sudo lvcreate -n apps-lv -L 14G webdata-vg
sudo lvcreate -n logs-lv -L 14G webdata-vg
sudo mkfs.ext4 /dev/webdata-vg/apps-lv
sudo mkfs.ext4 /dev/webdata-vg/logs-lv
```
<img width="901" height="669" alt="Screenshot 2026-05-26 174650" src="https://github.com/user-attachments/assets/ebeaf398-ae93-4c20-9858-4a34a567b4a9" />
<img width="813" height="470" alt="Screenshot 2026-05-26 174213" src="https://github.com/user-attachments/assets/36206f7d-17c4-4f35-ae30-bc3791eb7671" />
<img width="761" height="693" alt="Screenshot 2026-05-26 174155" src="https://github.com/user-attachments/assets/21478493-5d6a-4143-ae43-21962bc650a1" />
<img width="901" height="320" alt="Screenshot 2026-05-26 173713" src="https://github.com/user-attachments/assets/77005e07-9daf-4c27-bac7-429e81cb7771" />
<img width="906" height="259" alt="Screenshot 2026-05-26 173653" src="https://github.com/user-attachments/assets/dee261ee-8565-478f-b5eb-d162da6e2ead" />
<img width="906" height="354" alt="Screenshot 2026-05-26 173031" src="https://github.com/user-attachments/assets/a2a2326e-049b-45e7-9695-a20ff57efe51" />
<img width="883" height="309" alt="Screenshot 2026-05-26 172926" src="https://github.com/user-attachments/assets/9c637df0-0a79-4073-ad2b-bea649cefcc8" />
<img width="858" height="407" alt="Screenshot 2026-05-26 172655" src="https://github.com/user-attachments/assets/5cef83a8-9934-400c-825f-e085e60bc8c9" />
<img width="870" height="445" alt="Screenshot 2026-05-26 172613" src="https://github.com/user-attachments/assets/999db77e-a28f-4735-8a31-a773f920422f" />
<img width="834" height="309" alt="Screenshot 2026-05-26 172558" src="https://github.com/user-attachments/assets/02e72006-6272-4ea3-8ff2-51691f730e9c" />




Mount volumes:

``` bash
sudo mkdir -p /var/www/html
sudo mkdir -p /home/recovery/logs
sudo mount /dev/webdata-vg/apps-lv /var/www/html
sudo rsync -av /var/log/ /home/recovery/logs/
sudo mount /dev/webdata-vg/logs-lv /var/log
sudo rsync -av /home/recovery/logs/ /var/log
```
<img width="1239" height="358" alt="Screenshot 2026-05-26 195219" src="https://github.com/user-attachments/assets/ff1f5d38-7747-450b-8de0-528543329b5f" />
<img width="904" height="555" alt="Screenshot 2026-05-26 175746" src="https://github.com/user-attachments/assets/ca504fcb-a3ac-4bd9-bced-e9d907888920" />
<img width="902" height="458" alt="Screenshot 2026-05-26 175319" src="https://github.com/user-attachments/assets/4302bb07-d7e2-430c-8d3c-970511e9aa5d" />
<img width="816" height="699" alt="Screenshot 2026-05-26 174837" src="https://github.com/user-attachments/assets/e641e2b3-442f-4441-bbb0-faa7afcea7ec" />
<img width="779" height="190" alt="Screenshot 2026-05-26 174716" src="https://github.com/user-attachments/assets/4d41d704-36aa-417a-a921-90145a0a8199" />



Persist mounts:

``` bash
sudo blkid
sudo vi /etc/fstab
sudo mount -a
sudo systemctl daemon-reload
df -h
```
<img width="922" height="953" alt="Screenshot 2026-05-28 181334" src="https://github.com/user-attachments/assets/0e343836-9ded-49ce-b270-d044c7491008" />
<img width="953" height="937" alt="Screenshot 2026-05-28 181322" src="https://github.com/user-attachments/assets/020c1413-7c59-4fee-bd0d-2f7c4ade801b" />
<img width="938" height="918" alt="Screenshot 2026-05-28 180634" src="https://github.com/user-attachments/assets/033050c9-b531-4ff8-be61-05ffaf83edbe" />
<img width="936" height="689" alt="Screenshot 2026-05-28 180331" src="https://github.com/user-attachments/assets/1bf13e4a-176e-4f85-a9e5-85b328a65a73" />
<img width="921" height="527" alt="Screenshot 2026-05-28 175954" src="https://github.com/user-attachments/assets/cd091798-b217-4b96-ab59-8b70ef3447c7" />

------------------------------------------------------------------------

# Step 2 --- Prepare Database Server

Repeat the same LVM steps, but create:

``` bash
sudo lvcreate -n db-lv -L 14G webdata-vg
sudo lvcreate -n logs-lv -L 14G webdata-vg
sudo mkfs.ext4 /dev/webdata-vg/db-lv
sudo mkfs.ext4 /dev/webdata-vg/logs-lv
sudo mkdir /db
sudo mount /dev/webdata-vg/db-lv /db
```

Update `/etc/fstab` and verify with `df -h`.

------------------------------------------------------------------------

# Step 3 --- Install WordPress

``` bash
sudo dnf update -y
sudo dnf install -y httpd wget php php-fpm php-mysqlnd php-gd php-opcache
sudo systemctl enable --now httpd
sudo systemctl enable --now php-fpm
sudo setsebool -P httpd_execmem 1
sudo setsebool -P httpd_can_network_connect 1
```

Download WordPress:

``` bash
mkdir wordpress && cd wordpress
wget https://wordpress.org/latest.tar.gz
tar -xzf latest.tar.gz
cp wordpress/wp-config-sample.php wordpress/wp-config.php
sudo cp -R wordpress /var/www/html/
sudo chown -R apache:apache /var/www/html/wordpress
sudo chcon -Rt httpd_sys_rw_content_t /var/www/html/wordpress
sudo systemctl restart httpd
```

Edit `wp-config.php`:

``` text
DB_NAME=wordpress
DB_USER=myuser
DB_PASSWORD=mypass
DB_HOST=172.31.42.120
```
<img width="911" height="872" alt="Screenshot 2026-07-03 174559" src="https://github.com/user-attachments/assets/0c3c34b2-d18b-40e0-87ff-78d317f4243d" />

<img width="946" height="905" alt="Screenshot 2026-07-01 155905" src="https://github.com/user-attachments/assets/5657cec9-1420-494c-b782-1e667b245463" />


------------------------------------------------------------------------

# Step 4 --- Install MySQL

On the Database Server:

``` bash
sudo dnf install -y mysql8.4-server
sudo systemctl enable --now mysqld
```
<img width="1145" height="588" alt="Screenshot 2026-07-03 174655" src="https://github.com/user-attachments/assets/b4865573-722a-406b-a053-4f4dd9975ad3" />

------------------------------------------------------------------------

# Step 5 --- Configure Database

``` sql
CREATE DATABASE wordpress;
CREATE USER 'myuser'@'172.31.44.246' IDENTIFIED BY 'mypass';
GRANT ALL ON wordpress.* TO 'myuser'@'172.31.44.246';
FLUSH PRIVILEGES;
SHOW DATABASES;
```



<img width="887" height="714" alt="Screenshot 2026-07-01 163036" src="https://github.com/user-attachments/assets/f00e067f-7610-4531-84a1-95fb525ad178" />


------------------------------------------------------------------------

# Step 6 --- Security Groups

Database Server:

-   TCP 3306
-   Source: **172.31.44.246/32**

-   <img width="1550" height="287" alt="Screenshot 2026-07-03 172930" src="https://github.com/user-attachments/assets/efdbc71d-d6b3-4612-8599-357d7a8f4b19" />


Web Server:

-   HTTP TCP 80
-   Source: `0.0.0.0/0`

<img width="1550" height="287" alt="Screenshot 2026-07-03 172930" src="https://github.com/user-attachments/assets/2b6377a3-c984-491d-b7b3-35ffbbd6fc9d" />



------------------------------------------------------------------------

# Verification

Browse to:

``` text
http://44.245.157.133/wordpress
```
<img width="1830" height="932" alt="Screenshot 2026-07-01 164525" src="https://github.com/user-attachments/assets/0a04205f-6d60-4477-8d67-b78a8c2b8403" />

--------------------------

<img width="1875" height="954" alt="Screenshot 2026-07-01 164605" src="https://github.com/user-attachments/assets/4389d101-96f7-4d36-b390-f9e64f7f8e76" />

--------------------------

<img width="1175" height="569" alt="Screenshot 2026-07-01 165020" src="https://github.com/user-attachments/assets/99403128-94c4-4d45-aed1-defebea17b87" />

--------------------------

<img width="1753" height="999" alt="Screenshot 2026-07-01 165102" src="https://github.com/user-attachments/assets/3f71eb60-1845-4dd4-a06d-79d7be6408db" />


The WordPress installation wizard should appear. Complete the
installation with your preferred site title, administrator username,
password, and email.

## Outcome

The deployment was completed successfully using:

-   AWS EC2
-   Amazon EBS
-   LVM
-   Apache
-   PHP
-   MySQL 8.4
-   WordPress
-   RHEL 10.2
