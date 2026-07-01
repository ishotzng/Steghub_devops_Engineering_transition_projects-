# Web Solution With WordPress (Project 102)

This project demonstrates deploying a **WordPress-based web solution**
using two AWS EC2 instances running **Red Hat Enterprise Linux 10.2**.

-   **Web Server** -- Apache, PHP and WordPress
-   **Database Server** -- MySQL 8.4
-   **Storage** -- LVM configured with three 10 GiB EBS volumes on each
    server

## Project Architecture

-   **Web Server**
    -   Apache HTTP Server
    -   PHP
    -   WordPress
    -   LVM (`apps-lv`, `logs-lv`)
-   **Database Server**
    -   MySQL 8.4
    -   LVM (`db-lv`, `logs-lv`)
-   **Networking**
    -   Web Server Private IP: **172.31.44.246**
    -   Database Server Private IP: **172.31.42.120**
    -   WordPress connects to MySQL over port **3306**
-   **Public Access**
    -   Web Server Public IP: **44.245.157.133**

------------------------------------------------------------------------

# Step 1 --- Prepare Web Server

Launch a Red Hat EC2 instance and attach **three 10 GiB EBS volumes**.

> Replace the image paths below with your screenshots.

![](images/ec2.png)

## Verify attached disks

``` bash
lsblk
df -h
ls /dev
```

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

Mount volumes:

``` bash
sudo mkdir -p /var/www/html
sudo mkdir -p /home/recovery/logs
sudo mount /dev/webdata-vg/apps-lv /var/www/html
sudo rsync -av /var/log/ /home/recovery/logs/
sudo mount /dev/webdata-vg/logs-lv /var/log
sudo rsync -av /home/recovery/logs/ /var/log
```

Persist mounts:

``` bash
sudo blkid
sudo vi /etc/fstab
sudo mount -a
sudo systemctl daemon-reload
df -h
```

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

------------------------------------------------------------------------

# Step 4 --- Install MySQL

On the Database Server:

``` bash
sudo dnf install -y mysql8.4-server
sudo systemctl enable --now mysqld
```

------------------------------------------------------------------------

# Step 5 --- Configure Database

``` sql
CREATE DATABASE wordpress;
CREATE USER 'myuser'@'172.31.44.246' IDENTIFIED BY 'mypass';
GRANT ALL ON wordpress.* TO 'myuser'@'172.31.44.246';
FLUSH PRIVILEGES;
SHOW DATABASES;
```

------------------------------------------------------------------------

# Step 6 --- Security Groups

Database Server:

-   TCP 3306
-   Source: **172.31.44.246/32**

Web Server:

-   HTTP TCP 80
-   Source: `0.0.0.0/0`

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
