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

## Step 8: Checking the webserver

Great it’s working! Let’s finish the setup and move to the next step

![image.png](https://github.com/MohamedAwad9k8/AWS-Highly-Available-Web-App/blob/main/images/image%2030.png)

All set, and ready to go

![image.png](https://github.com/MohamedAwad9k8/AWS-Highly-Available-Web-App/blob/main/images/image%2031.png)

## Step 9: Creating an AMI out of the webserver

![image.png](https://github.com/MohamedAwad9k8/AWS-Highly-Available-Web-App/blob/main/images/image%2032.png)

Give the image a name and description

then scroll down and click “Create”

![image.png](https://github.com/MohamedAwad9k8/AWS-Highly-Available-Web-App/blob/main/images/image%2033.png)

## Step 10: Creating Launch Template

Choose Lauch Templates from the side menu

![image.png](https://github.com/MohamedAwad9k8/AWS-Highly-Available-Web-App/blob/main/images/image%2034.png)

Fill the name and description

![image.png](https://github.com/MohamedAwad9k8/AWS-Highly-Available-Web-App/blob/main/images/image%2035.png)

Choose the AMI we created 

![image.png](https://github.com/MohamedAwad9k8/AWS-Highly-Available-Web-App/blob/main/images/image%2036.png)

I will choose the free tier instance type for this project

![image.png](https://github.com/MohamedAwad9k8/AWS-Highly-Available-Web-App/blob/main/images/image%2037.png)

For the network settings we won’t specify subnets, but we will choose the security group

after that scroll down and click “Create”

![image.png](https://github.com/MohamedAwad9k8/AWS-Highly-Available-Web-App/blob/main/images/image%2038.png)

## Step 11: Creating Auto Scaling Group

Select Auto Scaling Groups from the side menu

![image.png](https://github.com/MohamedAwad9k8/AWS-Highly-Available-Web-App/blob/main/images/image%2039.png)

And Select create Auto Scaling Group

![image.png](https://github.com/MohamedAwad9k8/AWS-Highly-Available-Web-App/blob/main/images/image%2040.png)

Fill the name, and choose the launch template, then click “next”

![image.png](https://github.com/MohamedAwad9k8/AWS-Highly-Available-Web-App/blob/main/images/image%2041.png)

Choose the Project VPC, and choose the two private subnets, then “click “next”

![image.png](https://github.com/MohamedAwad9k8/AWS-Highly-Available-Web-App/blob/main/images/image%2042.png)

We will leave this at “No Load Balancer” for now, and click “next”

![image.png](https://github.com/MohamedAwad9k8/AWS-Highly-Available-Web-App/blob/main/images/image%2043.png)

We will set the desired number of instances to 2

![image.png](https://github.com/MohamedAwad9k8/AWS-Highly-Available-Web-App/blob/main/images/image%2044.png)

Also we will set the minimum to 2m and the maximum to 4

![image.png](https://github.com/MohamedAwad9k8/AWS-Highly-Available-Web-App/blob/main/images/image%2045.png)

Then we will set the scaling policy

so that the number of instances increase, when the CPUs utilization is 60%, click “next” after

![image.png](https://github.com/MohamedAwad9k8/AWS-Highly-Available-Web-App/blob/main/images/image%2046.png)

I will not use SNS notifications for this project, to try and keep it simple

Click “next” and finally click on “Create Auto Scaling group”

![image.png](https://github.com/MohamedAwad9k8/AWS-Highly-Available-Web-App/blob/main/images/image%2047.png)

## Step 12: Creating Target Group for the Load Balancer

Click on “Create Target Group”

![image.png](https://github.com/MohamedAwad9k8/AWS-Highly-Available-Web-App/blob/main/images/image%2048.png)

Choose Instance

![image.png](https://github.com/MohamedAwad9k8/AWS-Highly-Available-Web-App/blob/main/images/image%2049.png)

Fill the name, choose the desired protocol, and choose the Project VPC

Scroll down and click “next”

![image.png](https://github.com/MohamedAwad9k8/AWS-Highly-Available-Web-App/blob/main/images/image%2050.png)

Notice the newly two created webservers, from the Auto Scaling group

we will not choose targets here, scroll down and click “Create Target Group”

![image.png](https://github.com/MohamedAwad9k8/AWS-Highly-Available-Web-App/blob/main/images/image%2051.png)

## Step 13: Creating Application Load Balancer

Click on Create Load Balancer

![image.png](https://github.com/MohamedAwad9k8/AWS-Highly-Available-Web-App/blob/main/images/image%2052.png)

Choose Application Load Balancer

![image.png](https://github.com/MohamedAwad9k8/AWS-Highly-Available-Web-App/blob/main/images/image%2053.png)

Give it a name, choose Internet facing and IPv4

![image.png](https://github.com/MohamedAwad9k8/AWS-Highly-Available-Web-App/blob/main/images/image%2054.png)

In the network mapping, choose the two public subnets

![image.png](https://github.com/MohamedAwad9k8/AWS-Highly-Available-Web-App/blob/main/images/image%2055.png)

Choose the webserver security group

![image.png](https://github.com/MohamedAwad9k8/AWS-Highly-Available-Web-App/blob/main/images/image%2056.png)

Adjust this as follows

then scroll down and click “Create Load Balancer”

![image.png](https://github.com/MohamedAwad9k8/AWS-Highly-Available-Web-App/blob/main/images/image%2057.png)

## Step 14: Edit the Auto Scaling Group, and add the load balancer

![image.png](https://github.com/MohamedAwad9k8/AWS-Highly-Available-Web-App/blob/main/images/image%2058.png)

Optionally you can add “Name” tag so that the instances created from Auto Scaling group have name.

Press “Update” after.

![image.png](https://github.com/MohamedAwad9k8/AWS-Highly-Available-Web-App/blob/main/images/image%2059.png)

## Step 15: Testing the Load Balancer and Auto Scaling Group

Now go to the Load Balancer and choose “Resource Map” tab

Here you can see the instances appeared in the target group
Note: It’s also possible to create the Load Balancer first before creating the Auto Scaling Group, this way, we won’t have to go back and edit the Auto Scaling Group.

![image.png](https://github.com/MohamedAwad9k8/AWS-Highly-Available-Web-App/blob/main/images/image%2060.png)

Copy the DNS Name of the ELB to test it

![image.png](https://github.com/MohamedAwad9k8/AWS-Highly-Available-Web-App/blob/main/images/image%2061.png)

Voilà! The site is working great on Load Balancer, which is connected to the Auto Scaling Group instances in the Private Subnets.

This will ensure higher security for our servers.

![image.png](https://github.com/MohamedAwad9k8/AWS-Highly-Available-Web-App/blob/main/images/image%2062.png)

To test the Auto Scaling group let’s delete instances

![image.png](https://github.com/MohamedAwad9k8/AWS-Highly-Available-Web-App/blob/main/images/image%2063.png)

The webserver is of no use now, so I will terminate it.

I will also terminate the instance in AZ1, the expected behavior is the website will continue to work nicely through the ELB, and in few minutes, the auto scaling group will create a new instance to replace the unhealthy one.

![image.png](https://github.com/MohamedAwad9k8/AWS-Highly-Available-Web-App/blob/main/images/image%2064.png)

The resource map now shows only 1 healthy target

![image.png](https://github.com/MohamedAwad9k8/AWS-Highly-Available-Web-App/blob/main/images/image%2065.png)

The site is still working smoothly

![image.png](https://github.com/MohamedAwad9k8/AWS-Highly-Available-Web-App/blob/main/images/image%2066.png)

Few moments later, the Auto Scaling group created a new Instance in AZ1

![image.png](https://github.com/MohamedAwad9k8/AWS-Highly-Available-Web-App/blob/main/images/image%2067.png)

Let’s re-do the process with the the Instance in the AZ2

 

![image.png](https://github.com/MohamedAwad9k8/AWS-Highly-Available-Web-App/blob/main/images/image%2068.png)

![image.png](https://github.com/MohamedAwad9k8/AWS-Highly-Available-Web-App/blob/main/images/image%2069.png)

Great! the site is still working smootly.

![image.png](https://github.com/MohamedAwad9k8/AWS-Highly-Available-Web-App/blob/main/images/image%2070.png)

![image.png](https://github.com/MohamedAwad9k8/AWS-Highly-Available-Web-App/blob/main/images/image%2071.png)

A new instance has been launched in AZ2, automatically by the Auto Scaling Group

![image.png](https://github.com/MohamedAwad9k8/AWS-Highly-Available-Web-App/blob/main/images/image%2072.png)

The project is working as expected, and the highly available and scalable WordPress website is now ready for use!!!

Note: Here’s a diagram to help visualize the connections and configurations of ELB and Auto Scaling Group

![image.png](https://github.com/MohamedAwad9k8/AWS-Highly-Available-Web-App/blob/main/images/image%2073.png)

![image.png](https://github.com/MohamedAwad9k8/AWS-Highly-Available-Web-App/blob/main/images/image%2074.png)