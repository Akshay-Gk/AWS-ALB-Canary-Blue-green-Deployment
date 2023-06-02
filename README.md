# **_Canary and Blue-Green Deployment using ALB_** 

# Description:

ALB supports weighted traffic distribution so with the help of ALB we can do "Canary Deployment" and "Blue Green Deployment". Now i'm showing a simple demonstration of both type of deployment.


# Diagram:

![image](https://github.com/Akshay-Gk/AWS-ALB-Canary-Blue-green-Deployment/assets/112197849/3acd37c6-2db8-4c42-9f6d-e466f6e35416)



# Step 1 : Create Launch configuration

Log in ec2 console >> Choose "Launch configuration" under Auto Scaling >> Click "Create Launch configuration"

![image](https://github.com/Akshay-Gk/Migrate-EBS-volume-from-one-region-to-other/assets/112197849/5e287b90-cdf0-4f58-9450-74f954716b91)

_Enter name_ 
_Choose AMI_ - "Amazon Linux AMI"
_Choose Instance type_ - "t2-micro" - which comes under free tire

![image](https://github.com/Akshay-Gk/Migrate-EBS-volume-from-one-region-to-other/assets/112197849/4cb1092b-03d8-41de-bcbb-6b98af72b4cb)


_Under "Advance details" provide "user data"_

```
#!/bin/bash

yum install httpd php -y

cat <<EOF > /var/www/html/index.php
<?php
\$output = shell_exec('echo $HOSTNAME');
echo "<h1><center><pre>\$output</pre></center></h1>";
echo "<h1><center>my-app-version1</center></h1>"
?>
EOF


systemctl restart php-fpm.service httpd.service
systemctl enable php-fpm.service httpd.service
```

![image](https://github.com/Akshay-Gk/Migrate-EBS-volume-from-one-region-to-other/assets/112197849/bce8e35b-d2e9-4c58-aa53-a270056a3bf2)

_Choose or create a security group_

![image](https://github.com/Akshay-Gk/Migrate-EBS-volume-from-one-region-to-other/assets/112197849/fcc126f1-576d-412d-ac6a-39f27c541e38)

_Choose "key pair" 
_Click create "launch configuration"

![image](https://github.com/Akshay-Gk/Migrate-EBS-volume-from-one-region-to-other/assets/112197849/a5dac369-d76a-43b0-ba9e-7cb717d0b12d)


# Step 2 : Create Auto Scaling Group

_Choose Create an Auto Scaling group using the Launch configuration created_

![image](https://github.com/Akshay-Gk/Migrate-EBS-volume-from-one-region-to-other/assets/112197849/5d0a7e15-8209-404c-bb74-6573360df129)

_Choose "launch configuration" page, do the following:_
_Enter name 
_Select Launch Configuration_

![image](https://github.com/Akshay-Gk/Migrate-EBS-volume-from-one-region-to-other/assets/112197849/d47f6a31-f622-4fbb-8150-14975a55e9d4)

_Choose VPC_
_Choose Availability zone_

![image](https://github.com/Akshay-Gk/Migrate-EBS-volume-from-one-region-to-other/assets/112197849/d9b4e2d4-3d7a-434d-a0d1-0e1449e11ef6)

_choose "No Load Balancer" under Load Balancing_

![image](https://github.com/Akshay-Gk/Migrate-EBS-volume-from-one-region-to-other/assets/112197849/a29aa051-c070-4805-a298-f37ac6cc6089)

_Under Group Size choose,_
_Desired_ : 2
_Min_ : 2
_Max_ : 2

_Choose Scaling policies as "None"_

> `Note: Here we are using static Auto Scaling`

![image](https://github.com/Akshay-Gk/Migrate-EBS-volume-from-one-region-to-other/assets/112197849/3eaa8952-53ba-4c25-bb2a-515bfdc93c56)

_In Add tags_
_Enter Key : "Name" , Enter Value : "version1"_

![image](https://github.com/Akshay-Gk/Migrate-EBS-volume-from-one-region-to-other/assets/112197849/683072f4-827c-4497-81f1-f435e260411c)


# Step 3 : Create Target Group

_On the navigation pane, under LOAD BALANCING, choose Target Groups_
_Choose Target type as "Instances_
_Enter Target name_
_Choose protocol : "HTTP"_


![image](https://github.com/Akshay-Gk/Migrate-EBS-volume-from-one-region-to-other/assets/112197849/a327a0d0-9ad6-4811-a54a-3d753d8e0752)

_Choose "VPC"_
_Choose Health check protocoal - "HTTP"_
_Choose Health check path - "index.php"_
_Hit Next_
![image](https://github.com/Akshay-Gk/Migrate-EBS-volume-from-one-region-to-other/assets/112197849/49c6217f-e3e4-4f8b-bfad-1a3713ae1f12)

_Under "Register targets do not selct any instances. We will add the Target group to Auto scaling group"_
_Hit "Create"_

![image](https://github.com/Akshay-Gk/Migrate-EBS-volume-from-one-region-to-other/assets/112197849/e3e37873-59bf-494c-a8c8-999b4dc192e1)

_Here we can see the created "Target group"

![image](https://github.com/Akshay-Gk/Migrate-EBS-volume-from-one-region-to-other/assets/112197849/cc04cd09-e082-43fb-96ee-8c3e41c97f95)


# Step 3 : Attach Target group to Auto Scaling Group

_In Auto Scaling Group edit the existing "my-app-version1-asg" ASG_

![image](https://github.com/Akshay-Gk/Migrate-EBS-volume-from-one-region-to-other/assets/112197849/c7d286d6-a383-4836-a7ac-39bff116cc4b)

_Under Load balancing,_
_Enable "Application, Network or Gateway Load Balancer target groups"_ & _Select the Target group - "my-app-version1-tg"_

_Under health checks,_
_Enable "Turn on Elastic Load Balancing health checks"_
> `Elastic Load Balancing monitors whether instances are available to handle requests. When it reports an unhealthy instance, EC2 Auto Scaling can replace it on its next periodic check`

_Hit "Update"_

![image](https://github.com/Akshay-Gk/Migrate-EBS-volume-from-one-region-to-other/assets/112197849/1c248149-6fe2-4c2f-b0d4-4c2871e18fbb)


# Step 4 : Create Application Load balancer


_On the navigation pane, under LOAD BALANCING, choose Load balancers_
_Click "Create Load balancer"

![image](https://github.com/Akshay-Gk/Migrate-EBS-volume-from-one-region-to-other/assets/112197849/fa25b8cd-2976-4a7a-b18d-588036839c68)

_Under "Application Load Balancer" , Click "create"_

![image](https://github.com/Akshay-Gk/Migrate-EBS-volume-from-one-region-to-other/assets/112197849/cbe40f62-70c0-46d9-b1bc-bb26a90043c1)

_Enter name_ 
_Under Scheme ,Choose "Internet-facing"_
_Under IP address type ,Choose "IPv4"_

![image](https://github.com/Akshay-Gk/Migrate-EBS-volume-from-one-region-to-other/assets/112197849/8d10f194-b224-4456-8850-79ed31a5bb6c)

_Choose VPC_
_Select Subnet "ap-south-1a (aps1-az1)" & "ap-south-1b (aps1-az3)"

![image](https://github.com/Akshay-Gk/Migrate-EBS-volume-from-one-region-to-other/assets/112197849/ce70d2e4-b389-4995-be8b-dbf3daa63bd1)

_Select Secutiy group_

![image](https://github.com/Akshay-Gk/Migrate-EBS-volume-from-one-region-to-other/assets/112197849/a9d37202-2dc1-4f10-bc58-2e936c36286e)

_Under Listeners and routing , Choose Protocol "HTTPS"
_Under Default action , Choose Target Group - "my-app-version1-tg"_

![image](https://github.com/Akshay-Gk/Migrate-EBS-volume-from-one-region-to-other/assets/112197849/481ef392-48a5-4b69-bbae-e612ea7d9752)

_Under Secure listener settings , Choose your "Default SSL/TLS certificate" From **ACM**_
_Hit "Create Load balancer"_

![image](https://github.com/Akshay-Gk/Migrate-EBS-volume-from-one-region-to-other/assets/112197849/eaca4a0d-a08c-440f-8a0b-f1fa9d98f42e)

_Under Load balancer we can see the newly created Load balancer "my-app-alb"_

![image](https://github.com/Akshay-Gk/Migrate-EBS-volume-from-one-region-to-other/assets/112197849/73d78d2d-131c-49a9-93b2-5548bf448de9)


# Step 5 : Add Listener


_Once we Enable "my-app-alb" load balance we can find "listener" tab_
_Click "Add Listener"_

![image](https://github.com/Akshay-Gk/Migrate-EBS-volume-from-one-region-to-other/assets/112197849/fad4be5c-8214-42f2-96a0-f092cce2f463)


_Under Listener details , Choose HTTP: 80_
_Under " Redirect" ,Choose HTTPS : 443_

> `Note: Here we enabling auto redirection of HTTP Traffic to HTTPS`

![image](https://github.com/Akshay-Gk/Migrate-EBS-volume-from-one-region-to-other/assets/112197849/ed5ba199-26a2-42f7-8561-63b725bdd468)



# Step 6 : Manage Listener Rules

_Manage rules of "HTTPS" listener_
_Enable "HTTPS" listener and in **"Actions"** select "Manage rules"_

![image](https://github.com/Akshay-Gk/Migrate-EBS-volume-from-one-region-to-other/assets/112197849/fe90fa8f-b834-49b5-a60b-4f2fca2a18b2)

_Click plus sign and new add rule_
_You can find Default rule in green box_

![image](https://github.com/Akshay-Gk/Migrate-EBS-volume-from-one-region-to-other/assets/112197849/05d7c247-afec-4318-a426-bb252e3f4456)

_Click "insert rule"_
_Under **Add Condition** Select "host header" from the drop down_

![image](https://github.com/Akshay-Gk/Migrate-EBS-volume-from-one-region-to-other/assets/112197849/6bf1ab7b-536b-4233-9d9a-f45964c859fc)

_Add your "host name" under "Host header"_

![image](https://github.com/Akshay-Gk/Migrate-EBS-volume-from-one-region-to-other/assets/112197849/a6d0b10e-c598-4c97-9b65-bbfee6aacc50)


_under **Add action** Select "Forward to" from the drop down select "my-app-version1-tg: 1"_
_Hit tick mark then hit "save"_

![image](https://github.com/Akshay-Gk/Migrate-EBS-volume-from-one-region-to-other/assets/112197849/b364da54-ba5b-4a58-965b-766103d4b44a)


_You can see added rule_

![image](https://github.com/Akshay-Gk/Migrate-EBS-volume-from-one-region-to-other/assets/112197849/d7ca96ea-8d8f-4ea1-a5a3-e420dc71d9b2)

_Edit Default rule and set "fixed response"_
_Add a "site not found" text in Response body_

![image](https://github.com/Akshay-Gk/Migrate-EBS-volume-from-one-region-to-other/assets/112197849/41b2a74e-4a26-4cd7-8f5c-af0094ce6a8c)


# Step 6 : Add Record in Route53


_On the navigation pane, in **Route53** , choose **Hosted zones**_
_Choose **"create record"** Add a **"A record"** under domain_

![image](https://github.com/Akshay-Gk/Migrate-EBS-volume-from-one-region-to-other/assets/112197849/cbfb99ea-9dcb-49c8-a7f8-8a1a9ea5eede)

_Enter record name_
_Under route traffic choose:_ 
* _Alias to Application and classic load balancer_
* _Choose region_
* _Choose Application load balancer_

![image](https://github.com/Akshay-Gk/Migrate-EBS-volume-from-one-region-to-other/assets/112197849/fb607a61-ddc6-4d03-9c2a-f39ebe6705e0)


# Step 7 : Call URL 

_Once we call url we can find the version one page_

![image](https://github.com/Akshay-Gk/Migrate-EBS-volume-from-one-region-to-other/assets/112197849/9fd57d79-4dd8-4169-b010-fd7e78e394c2)

> `Note: Now we have create version 1`


> `Note: ALB supports weighted traffic distribution so with the help of ALB we can do "Canary Deployment" and "Blue Green Deployment". Now i'm showing a simple demonstration of both type of deployment`



# Canary Deployment:

# Diagram:

![image](https://github.com/Akshay-Gk/AWS-ALB-Canary-Blue-green-Deployment/assets/112197849/e7dc66d3-cd0c-4d1e-84d9-a4d731b1d30c)


Canary deployment is the practice of making staged releases. We roll out a software update to a small part of the users first, so they may test it and provide feedback. Once the change is accepted, the update is rolled out to the rest of the users

# Step 8 : Create a version 2 

* **_Repeat the same steps 1, steps 2 & steps 3 with name Version 2 instead of version 1_**

# Step 8 : 


