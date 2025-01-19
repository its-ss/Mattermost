# Secure Team Communication Solution on AWS

## Overview
This project provides a **secure, self-hosted team communication solution** using **Mattermost** and **MySQL** on AWS. It sets up a **custom VPC** with **public and private subnets**, ensuring compliance with data security policies.

## Architecture

### Components:
- **Custom VPC** with two subnets: Public & Private
- **MySQL Database** on a **private subnet**
- **Mattermost Application** on a **public subnet**
- **Bastion Host** for secure SSH access
- **NAT Gateway** for internet connectivity in private subnet
- **Security Groups** configured for required ports

### Architecture Diagram
![Architecture Diagram]([https://i.imgur.com/L5FqZnc.png](https://i.imgur.com/7WuvOmD.png))


---

## 🚀 Implementation Steps

### 🏗️ Step 1: VPC and Subnet Creation
1. Create a **VPC** with **CIDR block** `10.0.0.0/16`
2. Create a **Public Subnet** (`10.0.1.0/24`) and enable **Auto-Assign Public IP**
3. Create a **Private Subnet** (`10.0.2.0/24`)

### 🌍 Step 2: Internet Gateway and Routing
1. **Create & Attach an Internet Gateway** to the VPC
2. **Public Route Table** → Associate with the public subnet
3. **NAT Gateway** → Deploy in the public subnet & allocate an **Elastic IP**
4. **Private Route Table** → Associate with the private subnet

### ☁️ Step 3: EC2 Instance Setup
#### Application Server (Mattermost)
- **Amazon Linux 2** instance in **public subnet**
- Open security group ports: `80, 443, 22, 8065`
- Assign a key pair for **SSH access**

#### Database Server (MySQL)
- **Amazon Linux 2** instance in **private subnet**
- Open security group ports: `80, 443, 22, 3306`
- Use the same **key pair** for SSH access

---

## 🔧 Step 4: Install & Configure MySQL
1. **Connect to Database Server** via SSH:
   ```bash
   ssh -i your-key.pem ec2-user@<PRIVATE_IP>
   ```
2. **Install MySQL**:
   ```bash
   sudo yum update -y
   wget http://dev.mysql.com/get/mysql57-community-release-el7-9.noarch.rpm
   sudo yum localinstall mysql57-community-release-el7-9.noarch.rpm -y
   sudo yum install mysql-community-server -y --nogpgcheck
   sudo systemctl start mysqld.service
   ```
3. **Retrieve Temporary Password**:
   ```bash
   sudo grep 'temporary password' /var/log/mysqld.log | rev | cut -d" " -f1 | rev | tr -d "."
   ```
4. **Set a New MySQL Password**:
   ```sql
   ALTER USER 'root'@'localhost' IDENTIFIED BY 'Password42!';
   ```
5. **Complete the Setup**:
   ```bash
   wget https://d6opu47qoi4ee.cloudfront.net/install_mysql_linux.sh
   chmod 777 install_mysql_linux.sh
   sudo ./install_mysql_linux.sh
   ```

---

## 💬 Step 5: Install & Configure Mattermost
1. **Connect to Application Server via SSH**:
   ```bash
   ssh -i your-key.pem ec2-user@<PUBLIC_IP>
   ```
2. **Install Mattermost**:
   ```bash
   wget https://d6opu47qoi4ee.cloudfront.net/install_mattermost_linux.sh
   sudo yum install dos2unix -y
   sudo dos2unix install_mattermost_linux.sh
   chmod 700 install_mattermost_linux.sh
   sudo ./install_mattermost_linux.sh <PRIVATE_IP_OF_MYSQL_SERVER>
   ```
3. **Set Permissions**:
   ```bash
   sudo chown -R mattermost:mattermost /opt/mattermost
   sudo chmod -R g+w /opt/mattermost
   ```
4. **Start the Mattermost Server**:
   ```bash
   cd /opt/mattermost
   sudo -u mattermost ./bin/mattermost
   ```
5. **Access Mattermost**:
   ```
   http://<PUBLIC_IP_OF_APPLICATION_SERVER>:8065
   ```

---

## 🧹 Cleanup
Since AWS follows a **pay-per-use** model, **delete all resources in reverse order** after completing the project.

---

## 🔗 Resources
- [AWS VPC Documentation](https://docs.aws.amazon.com/vpc/)
- [Mattermost Official Site](https://mattermost.com/)
- [MySQL Official Site](https://www.mysql.com/)

---

## 👤 Author
[Suyash Shukla](https://github.com/yourgithub)  

---

### 📜 License
This project is licensed under the **MIT License**.

---
