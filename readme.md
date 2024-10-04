Here's a detailed and organized step-by-step markdown guide for the "DevOps Tooling Website Solution" project. I have added more clarity and comprehensive instructions for each step.

---

# DevOps Tooling Website Solution

In a previous project, you implemented a WordPress-based solution that can be used as a fully-fledged website or blog. Moving forward, we want to introduce a set of DevOps tools that will help your team in day-to-day activities in managing, developing, testing, deploying, and monitoring different projects.

This solution will consist of the following DevOps tools:

1. **Jenkins** - Open source automation server for building CI/CD pipelines.
2. **Kubernetes** - Container orchestration system for automating computer application deployment, scaling, and management.
3. **Jfrog Artifactory** - Universal repository manager supporting major packaging formats, build tools, and CI servers.
4. **Rancher** - Open source software platform for running and managing Docker and Kubernetes in production.
5. **Grafana** - Multi-platform open-source analytics and interactive visualization web application.
6. **Prometheus** - Open-source monitoring system with a dimensional data model and flexible query language.
7. **Kibana** - Free and open user interface that lets you visualize Elasticsearch data and navigate the Elastic Stack.

---

## Architecture

In this project, you will implement a tooling website solution that makes access to DevOps tools within the corporate infrastructure easily accessible. 

The architecture consists of the following components:

1. **Infrastructure**: AWS Cloud
2. **Web Server OS**: Red Hat Enterprise Linux 8
3. **Database Server OS**: Ubuntu 24.04 + MySQL
4. **Storage Server**: Red Hat Enterprise Linux 8 + NFS Server
5. **Programming Language**: PHP
6. **Code Repository**: GitHub

The infrastructure will follow a three-tier architecture pattern with stateless Web Servers sharing a common database and using Network File System (NFS) as shared storage.

### Architecture Diagram

```
                 ┌──────────────┐
                 │  NFS Server  │
                 └──────────────┘
                       │
          ┌────────────┼────────────┐
          │            │            │
     ┌─────────┐  ┌─────────┐  ┌─────────┐
     │ Web1    │  │ Web2    │  │ Web3    │
     └─────────┘  └─────────┘  └─────────┘
          │            │            │
   ┌─────────────────────────────────────┐
   │           MySQL Database Server     │
   └─────────────────────────────────────┘
```

---

## Step 1 - Prepare NFS Server

### 1. Launch an EC2 Instance
- Launch a new EC2 instance with **RHEL Linux 8 Operating System**.
- Choose an instance type based on your requirements (e.g., t2.micro for testing).

### 2. Configure LVM
- Create and attach a new EBS volume to your EC2 instance.
- SSH into your EC2 instance and configure LVM on the server:

```bash
# View attached disk
sudo fdisk -l

# Create physical volume
sudo pvcreate /dev/xvdf

# Create volume group
sudo vgcreate vg0 /dev/xvdf

# Create logical volumes
sudo lvcreate -L 2G -n lv-apps vg0
sudo lvcreate -L 2G -n lv-logs vg0
sudo lvcreate -L 2G -n lv-opt vg0
```

- Format the logical volumes as `xfs`:

```bash
sudo mkfs.xfs /dev/vg0/lv-apps
sudo mkfs.xfs /dev/vg0/lv-logs
sudo mkfs.xfs /dev/vg0/lv-opt
```

- Create mount points:

```bash
sudo mkdir -p /mnt/apps /mnt/logs /mnt/opt
```

- Mount the logical volumes:

```bash
sudo mount /dev/vg0/lv-apps /mnt/apps
sudo mount /dev/vg0/lv-logs /mnt/logs
sudo mount /dev/vg0/lv-opt /mnt/opt
```

### 3. Install NFS Server
```bash
sudo yum -y update
sudo yum install nfs-utils -y
sudo systemctl start nfs-server.service
sudo systemctl enable nfs-server.service
sudo systemctl status nfs-server.service
```

### 4. Configure NFS Exports
- Export the mounts for web servers' subnet CIDR to connect as clients.

```bash
# Set permissions
sudo chown -R nobody: /mnt/apps /mnt/logs /mnt/opt
sudo chmod -R 777 /mnt/apps /mnt/logs /mnt/opt

# Configure access
sudo vi /etc/exports
```

Add the following lines in `/etc/exports` (replace `<Subnet-CIDR>` with your actual CIDR, e.g., `172.31.32.0/20`):

```
/mnt/apps <Subnet-CIDR>(rw,sync,no_all_squash,no_root_squash)
/mnt/logs <Subnet-CIDR>(rw,sync,no_all_squash,no_root_squash)
/mnt/opt <Subnet-CIDR>(rw,sync,no_all_squash,no_root_squash)
```

- Save and exit the file.

```bash
sudo exportfs -arv
```

### 5. Open Ports for NFS
- Open TCP 111, UDP 111, and UDP 2049 in the Security Groups for NFS Server.

---

## Step 2 — Configure the Database Server

### 1. Launch an EC2 Instance
- Launch a new EC2 instance with **Ubuntu 24.04 Operating System**.

### 2. Install MySQL Server
```bash
sudo apt update
sudo apt install mysql-server -y
```

### 3. Configure MySQL Database
- Create a new database and user.

```sql
CREATE DATABASE tooling;
CREATE USER 'webaccess'@'%' IDENTIFIED BY 'password';
GRANT ALL PRIVILEGES ON tooling.* TO 'webaccess'@'%';
FLUSH PRIVILEGES;
```

- Update the `bind-address` in the `/etc/mysql/mysql.conf.d/mysqld.cnf` file to allow remote connections:

```
bind-address = 0.0.0.0
```

- Restart MySQL:

```bash
sudo systemctl restart mysql
```

### 4. Open Ports for MySQL
- Open TCP 3306 in the Security Groups for MySQL Server.

---

## Step 3 — Prepare the Web Servers

### 1. Launch an EC2 Instances
- Launch a new EC2 instances with **RHEL 8 Operating System**.

### 2. Configure NFS Client on the Web Server
```bash
sudo yum install nfs-utils nfs4-acl-tools -y
sudo mkdir /var/www
sudo mount -t nfs -o rw,nosuid <NFS-Server-Private-IP-Address>:/mnt/apps /var/www
```

- Verify that NFS was mounted successfully:

```bash
df -h
```

- To persist the mount on reboot:

```bash
sudo vi /etc/fstab
```

Add the following line:

```
<NFS-Server-Private-IP-Address>:/mnt/apps /var/www nfs defaults 0 0
```

### 3. Install Apache and PHP on Each Web Server
```bash
sudo yum install httpd -y
sudo dnf install https://dl.fedoraproject.org/pub/epel/epel-release-latest-8.noarch.rpm
sudo dnf install dnf-utils http://rpms.remirepo.net/enterprise/remi-release-8.rpm
sudo dnf module reset php
sudo dnf module enable php:remi-7.4
sudo dnf install php php-opcache php-gd php-curl php-mysqlnd
sudo systemctl start php-fpm
sudo systemctl enable php-fpm
setsebool -P httpd_execmem 1
```

- Repeat these steps for the remaining 2 Web Servers.

### 4. Mount Apache Logs to NFS Server
```bash
sudo mkdir /var/log/httpd
sudo mount -t nfs -o rw,nosuid <NFS-Server-Private-IP-Address>:/mnt/logs /var/log/httpd
```

### 5. Deploy Tooling Website
- Fork the tooling source code from Steghub GitHub Account.
- Clone it to each Web Server:

```bash
git clone https://github.com/<your-username>/tooling.git /var/www/html
```

### 6. Update Website Configuration
- Modify the database configuration in `/var/www/html/functions.php`:

```php
$servername = "<Database-Private-IP>";
$username = "webaccess";
$password = "password";
$dbname = "tooling";
```

- Apply the `tooling-db.sql` script:

```bash
mysql -h <Database-Private-IP> -u webaccess -p tooling < tooling-db.sql
```

### 7. Open Website in Browser
- Open the website using `http://<Web-Server-Public-IP-Address>/index.php` and ensure you can log in with the credentials you created.

---
