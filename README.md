# RDS Database Creation

## Overview
- Use AWS Management Console to create RDS subnet groups
- Create RDS database clusters
- Manage access to RDS clusters using security group rules
- Connect to RDS cluster and edit tables

## Important note:
- Below notes are a combination of lab instructions, and my edits.
- Source: https://cloudacademy.com/
    - Credit where credit is due :)

## Creating RDS Subnet Group
- Before launching actual RDS instances, you need to configure a **DB Subnet Group**.
- Subnets
    - **Subnets are segments of a VPC's IP address range** that allow you to group your resources based on security and operational needs. 
    - A DB Subnet Group is a collection of subnets (typically private) that you create in a VPC and designate for your DB instances. 
    - Each DB subnet group should have subnets in **at least two Availability Zones** in a given region.
- When creating a DB instance in a VPC, you must select a DB subnet group:
    - Amazon RDS uses that DB subnet group and your preferred Availability Zone to select a subnet and an **IP address within that subnet to associate with your DB instance**. 
    - When Amazon RDS creates a DB instance in a VPC, it **assigns a network interface to your DB instance by using an IP address selected from your DB Subnet Group**.
        -  If the primary DB instance of a Multi-AZ deployment fails, Amazon RDS can promote the corresponding standby and subsequently create a new standby using an IP address from an assigned subnet in one of the other Availability Zones.

### Steps
- Go to RDS service, select Subnet Groups
    - Set Name, Description, VPC
    - Choose availability zones, and the subnets that are available you want (should be private)

## Set up VPC Security Group Rules for Connceting to RDS instance
- We use an EC2 instance to run queries against the RDS database in upcoming Lab Steps. In order to allow incoming traffic from EC2 instances to the RDS instance inside the same VPC.
    - The rules of a Security Group control the inbound traffic that's allowed to reach the instances that are associated with the security group and the outbound traffic that's allowed to leave them. By default, security groups allow all outbound traffic and deny all inbound traffic.
- Go to VPC, Security Groups
    - Name, Description, add inbound rules "add rule"
        - Type: MySQL/Aurora
        - Protocol: TCP
        - Port: 3306
        - Source (custome): 172.31.0.0/16

## Creating a database using RDS
- Amazon RDS is designed for developers or businesses who require the full features and capabilities of a relational database or who wish to migrate existing applications and tools that utilize a relational database. It gives you access to the capabilities of a MySQL, Oracle, Microsoft SQL Server, MariaDB, Amazon Aurora, or PostgreSQL database engine.
- The RDS service is fully managed by Amazon. RDS will make sure that the database software stays up-to-date with the latest patches and any faulty compute instance powering your database deployment will be automatically replaced in the event of a hardware failure. You can automatically or manually create database snapshots and easily scale your infrastructure up or down using the AWS Management Console.

- This example is MySQL, default setting
- General
    - DB instance identifier: name-of-db
    - Master username: set master un
    - Master password: set strong pw
- DB Instance Size
    - Include previous generation classes toggle
    - DB Instance Class: Select Burstable classes and select db.t2.micro
- Storage: Enable Storage Autoscaling: Unchecked
- VPC: select the VPC we created
- Additional configuration to automatic backups, monitoring, logging, deletion-protection

- For production workloads, you can consider using provisioned IOPS as the Storage type to guarantee consistent throughput levels. Storage autoscaling can also be useful in production to avoid manually scaling up the database's storage when it breaches a certain threshold. Not needed here.

## Starting an AWS Systems Manager Browser Shell Session
### Session Manager:
    - Session Manager is part of AWS Systems Manager suite of tools for gaining operational insights and taking action on AWS resources. 
    - Session Manager gives you **browser-based shell access to EC2 instances running the Systems Manager agent**. 
    - Both Windows and Linux instances are supported. 
    - **Session manager provides secure access to instances without the need to distribute passwords or SSH keys**. 
    - **Session Manager also allows you to connect to instances without having to open any inbound ports**. 
    - **All communication is encrypted and IAM policies can restrict access to sessions running in Session Manager.**

    - We will use Session Manager to start a session on an EC2 instance running in your

### Steps:
    - AWS, go to Systems Manager service > Session Manager
    - click start session
    - select the EC2 instance that we want to use
    - start session
    - Now have a session to open a browser-based shell on an EC2 instance using AWS Systems Manager Session Manager.

## Connecting to RDS and Creating a Database Table
    - Our RDS instance is ready and accessible from any EC2 instance created within the same VPC
    - So we use Session Manager session to connect to the database. 
    - In this Lab Step, we will connect to your RDS instance and create a database table.

### Steps:
    - In your Session Manager shell session, enter the following command to change to the default Amazon Linux user (ec2-user) running in a bash shell:
        - **sudo -i -u ec2-user**
    - Install mysql client
        - **sudo yum -y install mysql**
    - You are ready to connect to your RDS instance using the RDS endpoint URL. Get from RDS console.
    - in session run:
        - **mysql -h <<your.endpoint.aws.com>> -u <Username> -p <initialdatabasename>**
    - Now in the DB can run commands likw:
        ** CREATE TABLE laboratory ( id INT, name VARCHAR(100) );**
    - **quit;** to quit

## Deleting an RDS Database
- In RDS console, disable deletion-protection if needed, then delete.

# Resources
- https://cloudacademy.com/ Lab
