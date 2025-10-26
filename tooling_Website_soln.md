## Devops Tooling Website Solution for 3 -Tier Architecture
AS a DevOps engineer there are tools that must have.In this project we are going to setup a website and build it using the DevOps tools such as 
- Jenkins
- Kubernetes
- Rancher
- Grafana
### Prerequisite

- Infarastructure: AWS
- Webserver Linux: RedHat Enterprise linux 9
- Database Server: ubuntu 24.04  + MySql
- Storage Server: RedHat Linux Enterpriser 9
- Programing Language: PHP
- Code Repository: Github

### Step 1. Setting up NFS Server
Navigate to AWS dashboard and choose a new EC2 Instance
choosing RHEL Linux 9 OS.
Launch the instance and when ready ssh into it.
![Images](NFS-Server.png)
sshing into the server
![Images](SSH%20into%20ec2-Instance.png)
### Step 2. Configuring LVM on the server
create 3 volumes of 10Gb each and attach to ec2 instance.
![Images](NFS%20instance%20attaching%20Volumes.png)
Partitioning the disks
![Images](Partitioning%20xfs%20volume.png)
Instaslling lvm 

```sh
    sudo yum install lvm2
```
![Images](INstall%20lvm2.png)
Creating volume groups using pvcreate 
![Images](pvcreate%20vol.png)

Creating 3 logical volumes. lv-opt, lv-apps and lv-logs
formating the disks as xfs
![Images](xfs%20volume.png)

Create mount points on /mnt directory for logical volumes.
![Images](Mounting%20directories.png)
Verifying mounting
![Images](Final%20Mount%20Done.png)
### Step 3. Installing NFS Server
Installing NFS Server on Storage server
```sh
     sudo yum -y update
     sudo yum install nfs-utils -y
     sudo systemctl start nfs-server.service
     sudo systemctl enable nfs-server.service
     sudo systemctl status nfs-server.service
```
![Images](Installing%20NFS%20server%20in%20ec2.png)

### Step 4. Exporting the mount for Webservers Subnet cidr to connect as clients.
![Images](subnet_ids.png)
getting the subnet from ec2 instance networking tabs and updating the exports files with the subnet cidr
![Images](exports-ips.png)

### Step 5. Configuring Security Groups
Checking which port is used by NFS and allowing access by updating inbound rules with the shown ports
```sh
    rpcinfo -p | grep nfs
```
![Images](checking_ports.png)
Updating Inbound rules
![Images](allowing%20inbound%20rules.png)
### Step 6. Configure Database Server
Installing MYSQL Server on DBserver and creating tooling database, user and credentials
![Images](Creating%20user%20on%20DB.png)

### Step 7. Preparing Web Server
Launching an EC2 Instance with RHEL 9  OS 
![Images](web-server-instance.png)
Install NFS client
```sh
   sudo yum install nfs-utils nfs4-acl-tools -y
```
Mount /var/www and target the NFS server export for app
```sh
    sudo mkdir /var/www
    sudo mount -t nfs -o rw,nosuid <NFS-Server-IP>:/mnt/apps /var/www
```
![Images](preparing%20web%20server%201.png)

Install Remi's repository, Apache and PHP
```sh
    sudo yum install httpd -y
    sudo dnf install https://dl.fedoraproject.org/pub/epel/epel-release-latest-8.noarch.rpm
    sudo dnf install dnf-utils http://rpmd.remirepo.net/enterprise/remi-release-8.rpm
    sudo dnf module reset pho
    sudo dnf module enable php:remi-7.4
    sudo dnf install php php-opcache php-gd php-curl php-mysqlnd
    sudo systemctl start php-fpm
    sudo systemctl enable php-fpm
```
![Images](confirming%20apache%20files.png)

The same steps to be done for server 2 and server 3
### Step 8. Fork the tooling source code from StegHUb Github Account
Navigate to Steghubb repo using the link
https://github.com/StegTechHub/tooling

Click on Fork to fork the repo
![Images](Forking%20Repo.png)
cloning the repo
![Images](git%20cloning%20.png)

### Step 9. Accesiing the Website 
on the webserver instance get the public ip and on the browser open the link
```sh
   http://Web-server-Public-ip/index.php
```
![Images](steghub%20login%20page.png)

