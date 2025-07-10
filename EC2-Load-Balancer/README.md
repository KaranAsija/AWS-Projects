## EC2 Load Balancer Setup 

Project Overview
This project demonstrates how to configure an **Application Load Balancer (ALB)** in AWS to distribute incoming traffic across multiple EC2 instances for high availability and fault tolerance.

---

## Technologies Used
- **AWS EC2**
- **AWS Application Load Balancer**
- **AWS Security Groups**
- **Amazon Linux 2 AMI**
- **Nginx Web Server**

---

## Objectives

 Launch multiple EC2 instances in different Availability Zones.  
 Install and configure Nginx web server.  
 Create an Application Load Balancer to distribute traffic.  
 Verify load balancing functionality via browser.

---

📝 Steps Performed

### **Step-1: Launch EC2 Instances and install Nginx Web Server in Instances**

Move to EC2 instance console and Click on Launch Instance.

For instance `Name` enter instance-01.

Select the `AMI` as Ubuntu.

Select the `Instance Type` as t2.micro.

Select a `key pair`.

In the Network Settings section click on `edit` and select the Availability as `ap-south-1a`.

In the `Inbound Security Rules` allow SSH and HTTP from anywhere(0.0.0.0/0).

In the `Advanced Settings` go to Userdata and paste the code:

```bash
#!/bin/bash 

apt-get update 
apt-get install nginx -y

echo "This is Server 1 $(hostname) - EC2 Load Balancer Demo"> /var/www/html/index.html

```

Then click on `Create Instance`.

Follow the above process again and create two more instance of name instance-02 and instance-03 in availability zone `ap-south-1b` and `ap-south-1a` respectively and use the same userdata for these instances.

### **Step-2: Create Target Group**

Move to the `Target Groups` console 

Click on `Create Target Group`.

Select the `Target type` as Instances.

For `Target Group Name` enter ALB-tg.

In protocol version select `HTTP1`.

In the Health Check section go to Advanced health check settings.

Set `Healthy threshold` as 4.

Set `Unhealthy threshold` as 3.

Set `Timeout` as 5 seconds.

Set `Interval` as 30 seconds.

Then click on `Next`.

Then select all three EC2 instances that are instance-01, instance-02, and instance-03.

Then click on `Include Pending Below` and then click on `Create Target Group`.

### **Step-3: Create Application Load Balancer**

Move to the `Load Balancer` console.

Then click on `Create load balancer`.

Then create `Application Load Balancer`.

For load balancer `Name` enter ALB.

Scroll down to the Network Mapping section and Select all three availability zone so that the load balancer can send the client request to these availability zone where the instances are created.

In the security group section click on `Create a new Security Group`.

For `Security group name` enter ALB-sg and in inbound rules allow HTTP traffic from anywhere (0.0.0.0/0).

Then go back to the security group section and add this security group to this load balancer.

Then in the Listener and Routing section, in the default action select the target group that is `ALG-tg`.

Scroll down and click on `Create Load Balancer`.

In the Load Balancer section copy the DNS name of your loadbalancer that is `ALB` and paste the DNS name on the browser.

Refresh the page and you will see that the load balancer is routing our request to different server almost every time.


### **Step-4: Allow HTTP traffic only from Load Balancer to Instance**

Move to the EC2 instance console and select the `instance-01` and go to the security section.

Click on the link of Security groups and then click on `Edit inbound rules`.

Delete the HTTP security rule and then click on add rule.

Select the type as HTTP and in the source section select the security group `ALB-sg` that you created for the Application load balancer.

Now only this instance will allow HTTP traffic from the load balancer only.

Do the above for the remaining two instances that are `instance-02` and `instance-03`.









