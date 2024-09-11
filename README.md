# AWS-Highly-Available-Web-App

# Overview

This project involves creating a highly available and scalable wordpress website, leveraging various AWS services including EC2 instances, RDS SQL database, Autos-Scaling groups, ELB, Route 53 and VPC.

# Architecture Diagram

![image](https://github.com/user-attachments/assets/83fe0ee1-c0b7-420f-8fa2-51bd1db61b82)


---
# AWS Services Used

- **Amazon EC2**: Used to host the web servers.
- **Amazon RDS**: MySQL database for the Wordpress website.
- **Amazon VPC**: Network and Subnets for the project architecture.
- **AWS Auto Scaling Group**: Auto Scaling Group to allow for highly available and scalable architecture.
- **AWS ELB**: Application Load Balancer allow for highly available and scalable architecture.
- **AWS Route 53**: DNS to organize traffic as desired, allows for custom domain use, and supports the highly available architecture.


# Step-by-Step Setup  


## Step 1: Setting up the VPC

let’s create a new VPC for the project

![image.png](https://github.com/MohamedAwad9k8/AWS-Highly-Available-Web-App/blob/main/images/image.png)

Choosing the desired number of AZs, Public and Private subnets

![image.png](https://github.com/MohamedAwad9k8/AWS-Highly-Available-Web-App/blob/main/images/image%201.png)

Optionally we can configure the CIDR blocks for the subnets as desired
there’s no need to do that here.

Just remember to keep the CIDR blocks for different subnets distinct (no intersections) And make sure the subnet has enough IP addresses for your own usage.

![image.png](https://github.com/MohamedAwad9k8/AWS-Highly-Available-Web-App/blob/main/images/image%202.png)

I decided to adjust the CIDR block to allow for only 16 IPs per subnet, which is still more than what I need. 

![image.png](https://github.com/MohamedAwad9k8/AWS-Highly-Available-Web-App/blob/main/images/image%203.png)

Keep in mind, AWS reserves 5 IPs from a subnet, so I will be left with only 11 available IPs here.

Here’s the explanation from AWS documentation:

For example, in a subnet with CIDR block `10.0.0.0/24`, the following five IP addresses are reserved:

- 10.0.0.0: Network address.
- 10.0.0.1: Reserved by AWS for the VPC router.
- 10.0.0.2: Reserved by AWS. The IP address of the DNS server is the base of the VPC network range plus two. For VPCs with multiple CIDR blocks, the IP address of the DNS server is located in the primary CIDR. We also reserve the base of each subnet range plus two for all CIDR blocks in the VPC. For more information, see [Amazon DNS server](https://docs.aws.amazon.com/vpc/latest/userguide/AmazonDNS-concepts.html#AmazonDNS).
- 10.0.0.3: Reserved by AWS for future use.
- 10.0.0.255: Network broadcast address. We do not support broadcast in a VPC, therefore we reserve this address.

Check the preview, and click “Create VPC”

![image.png](https://github.com/MohamedAwad9k8/AWS-Highly-Available-Web-App/blob/main/images/image%204.png)

## Step 2: Adjusting the RouteTables and the NACLs if needed

For this project we only need the basic configuration which comes with the VPC after it’s created

## Step 3: Creating Security Groups

Head to Security Groups tab under security on the left menu
Choose “create security group”

Make sure to choose the project vpc, then add a name and a description for the SG.

![image.png](https://github.com/MohamedAwad9k8/AWS-Highly-Available-Web-App/blob/main/images/image%205.png)

Add the rule and press “Create”

![image.png](https://github.com/MohamedAwad9k8/AWS-Highly-Available-Web-App/blob/main/images/image%206.png)

next we will create the webserver SG

![image.png](https://github.com/MohamedAwad9k8/AWS-Highly-Available-Web-App/blob/main/images/image%207.png)

Note: we will choose the command-host-sg as the source for the incoming ssh traffic.

Add the rules and click on “create”

![image.png](https://github.com/MohamedAwad9k8/AWS-Highly-Available-Web-App/blob/main/images/image%208.png)

We will do the same for the database security group

Here, we will set the source to be the Security Group we just created, and click “create” after.

![image.png](https://github.com/MohamedAwad9k8/AWS-Highly-Available-Web-App/blob/main/images/image%209.png)

## Step 4: Creating the command host EC2 instance

Why create a command host?
A command host or a bastion host is a server used to manage access to an internal or private network from an external network. It's a best practice that provides better security for the network and the servers.
To connect to a web server, we will have to SSH into the Command Host, and from it, we can SSH to the web server. (If needed)

Head to EC2 service, and click on “Launch Instance”

I will just choose the default AMI, and an instance type that’s free tier eligible for the purpose of this project.

![image.png](https://github.com/MohamedAwad9k8/AWS-Highly-Available-Web-App/blob/main/images/image%2010.png)

Create a keypair to login to the instance through SSH later

![image.png](https://github.com/MohamedAwad9k8/AWS-Highly-Available-Web-App/blob/main/images/image%2011.png)

Edit the Network Settings to choose the project VPC, public subnet 1, and the command host SG.

Also make sure to turn on the Auto Assign Public IP.

![image.png](https://github.com/MohamedAwad9k8/AWS-Highly-Available-Web-App/blob/main/images/image%2012.png)

Scroll down and click “Launch Instance”

## Step 5: Creating RDS Subnet Group

This is required by RDS databases
We need to make sure the subnet group is using the same Project VPC

Head to the RDS service page, and select subnet groups

![image.png](https://github.com/MohamedAwad9k8/AWS-Highly-Available-Web-App/blob/main/images/image%2013.png)

![image.png](https://github.com/MohamedAwad9k8/AWS-Highly-Available-Web-App/blob/main/images/image%2014.png)

Choose the two private subnets, you can double check the CIDR blocks from the VPC —> Subnets page

![image.png](https://github.com/MohamedAwad9k8/AWS-Highly-Available-Web-App/blob/main/images/image%2015.png)

Scroll down and click on “Create”

## Step 6: Creating the RDS MySQL Database

Go to databases, and click on create database

![image.png](https://github.com/MohamedAwad9k8/AWS-Highly-Available-Web-App/blob/main/images/image%2016.png)

Choosing Standard and MySQL

![image.png](https://github.com/MohamedAwad9k8/AWS-Highly-Available-Web-App/blob/main/images/image%2017.png)

Choose the version and the template

![image.png](https://github.com/MohamedAwad9k8/AWS-Highly-Available-Web-App/blob/main/images/image%2018.png)

Note: I should be choosing this, but I will go with the free tier, which allows me to use single DB instance only.

![image.png](https://github.com/MohamedAwad9k8/AWS-Highly-Available-Web-App/blob/main/images/image%2019.png)

Next enter a username and a password for the database, and adjust the DB identifier if needed

![image.png](https://github.com/MohamedAwad9k8/AWS-Highly-Available-Web-App/blob/main/images/image%2020.png)

![image.png](https://github.com/MohamedAwad9k8/AWS-Highly-Available-Web-App/blob/main/images/image%2021.png)

In Connectivity choose the Project VPC, and the newly created DB subnet group

![image.png](https://github.com/MohamedAwad9k8/AWS-Highly-Available-Web-App/blob/main/images/image%2022.png)

Finally choose the security group

![image.png](https://github.com/MohamedAwad9k8/AWS-Highly-Available-Web-App/blob/main/images/image%2023.png)

Add initial database name

![image.png](https://github.com/MohamedAwad9k8/AWS-Highly-Available-Web-App/blob/main/images/image%2024.png)

Finally scroll down and click on “create database”

Wait the db till it’s created, then copy it’s endpoint url, so that we can use it to connect the EC2 instance to it.

![image.png](https://github.com/MohamedAwad9k8/AWS-Highly-Available-Web-App/blob/main/images/image%2025.png)

It will look like this

![image.png](https://github.com/MohamedAwad9k8/AWS-Highly-Available-Web-App/blob/main/images/image%2026.png)

## Step 7: Creating the webserver EC2 instance

This should be the same steps as the command host, except for the network settings

Here we will choose public subnet 2 and webserver-sg

Note: Also make sure to create a keypair, and to choose it .pem, as it would be used from Linux machine if needed.

![image.png](https://github.com/MohamedAwad9k8/AWS-Highly-Available-Web-App/blob/main/images/image%2027.png)

Scroll down and expand advanced details, then scroll down to user data

Here we can type, paste, or upload a script that will run at the instance launch

![image.png](https://github.com/MohamedAwad9k8/AWS-Highly-Available-Web-App/blob/main/images/image%2028.png)

I will upload a script to automate the process of installing and configuring wordpress for me,

![image.png](https://github.com/MohamedAwad9k8/AWS-Highly-Available-Web-App/blob/main/images/image%2029.png)

The script is uploaded, now let’s launch the webserver instance.