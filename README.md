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



