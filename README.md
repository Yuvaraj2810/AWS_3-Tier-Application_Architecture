# AWS 3-Tier Application Architecture

> **The cloud is not just a technology, it's a revolution that empowers us to innovate, scale, and transform our ideas into reality.**

## VPC Configuration
1. **Create VPC** – `10.0.0.0/16`

## Subnets
2. **Subnets:**
   - **Public-Subnet-1** – `10.0.1.0/24` (AZ-a)
   - **Public-Subnet-2** – `10.0.2.0/24` (AZ-b)
   - **Private-Subnet-1** – `10.0.3.0/24` (AZ-a)
   - **Private-Subnet-2** – `10.0.4.0/24` (AZ-b)
   - **DB-Subnet-1** – `10.0.5.0/24` (AZ-a)
   - **DB-Subnet-2** – `10.0.6.0/24` (AZ-b)

## Route Tables
3. **Route Tables:**
   - **Public-RT** – Associate Public Subnet 1 & 2
   - **Private-RT** – Associate Private Subnet 1 & 2

## Internet Gateway
4. **Create Internet Gateway** - Attach to the VPC created.

## NAT Gateway
5. **Create a NAT Gateway** (Use Public Subnet 2)

## Route Configuration
6. **In the Public-RT** – Add a route to the Internet:
   - `0.0.0.0/0` – IGW (Internet Gateway)
7. **In the Private-RT** – Add a route to the Internet:
   - `0.0.0.0/0` – NAT (NAT Gateway)

## Security Groups
8. **Security Groups:**
   - **LoadBalancer-SG** – Allow HTTP & HTTPS from `0.0.0.0/0`
   - **App-Server-SG** – Allow HTTP from LoadBalancer-SG
   - **DB-SG** – Allow MySQL from App-Server-SG

## IAM Role
9. **Create an IAM Role called EC2SSMAgent** – Add the policy `AmazonSSMManagedInstanceCore`

## Application Server
10. **Create an Application-Server:**
    - Launch Instance
    - Select your VPC and Private-Subnet-1
    - Select Existing SG – Application-SG
    - Under Advanced Details – IAM Instance Profile – Choose the Role created (EC2SSMAgent)

## Install HTTPD and MariaDB
11. **Install HTTPD, Start & Enable it:**
    ```bash
    sudo su
    yum install httpd -y
    systemctl start httpd
    systemctl enable httpd
    dnf install mariadb105-server
    yum install php php-mysqli
    ```

## RDS Configuration
12. **Go to RDS & Create Subnet Group** – Select AZ a & b, Select your DB Subnets.

## Database Setup
13. **Login to Database:**
    ```bash
    mysql -h <db-hostname> -u <username> -p
    show databases;
    create database wordpress;
    show databases;
    exit
    ```

## WordPress Installation
14. **Download WordPress:**
    ```bash
    cd /var/www/html
    wget https://wordpress.org/latest.zip
    unzip latest.zip
    rm latest.zip
    mv wordpress/* .
    ```

15. **Rename config-sample file:**
    ```bash
    mv wp-config-sample.php wp-config.php
    vi wp-config.php
    ```
    - Set Database Parameters:
      - `dbname`
      - `db username`
      - `db password`
      - `db endpoint`

16. **Restart HTTPD:**
    ```bash
    systemctl restart httpd
    ```

## Create AMI
17. **Create an AMI of your Application Server**

## Target Group and Load Balancer
18. **Create a Target Group** – Select your VPC and Add Application Server
19. **Create an Application Load Balancer** – Select your VPC and Public Subnet 1 & 2, Select the Load Balancer Security Group.

## Verify Application
20. **Check the Load Balancer DNS** and confirm whether the Application is working.

## AutoScaling Group
21. **Create AutoScaling Group**

## Monitoring and Notifications
22. **Create SNS and CloudWatch**

## Domain and SSL
23. **Create Route-53**
24. **Go to Certificate Manager** – Create a new Certificate for your domain
25. **Add Name Servers to Route 53**
26. **Edit Load Balancer Listener to HTTPS**

## Acknowledgements
Thanks to all contributors and the AWS community for their support and resources.

---
