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

![image](https://github.com/user-attachments/assets/b7ecf6af-6bae-410d-baf9-c7ab4e6555c5)
Choosing the desired number of AZs, Public and Private subnets
