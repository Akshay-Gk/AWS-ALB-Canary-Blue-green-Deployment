# **_Host-Header based routing in ALB_** 

# Description:

# Diagram:

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

![image](https://github.com/Akshay-Gk/Migrate-EBS-volume-from-one-region-to-other/assets/112197849/1c248149-6fe2-4c2f-b0d4-4c2871e18fbb)








