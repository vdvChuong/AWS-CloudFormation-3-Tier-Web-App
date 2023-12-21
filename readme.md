# Multi-Tier Web Application Deployment with CloudFormation

## Overview

This AWS CloudFormation templates deploys a multi-tier web application that includes EC2 instances for the application layer, an RDS instance for the database layer, and an Elastic Load Balancer (ELB) for load balancing.  Two applications, WordPress and PHP, are deployed.

# AWS CloudFormation Template Explanation

## Parameters
- **VpcId:** The VPC ID of the existing Virtual Private Cloud.
- **Subnets:** List of subnet IDs in the VPC.
- **KeyName:** Name of an existing EC2 KeyPair for SSH access.
- **InstanceType:** Type of EC2 instance for the web server.
- **DBClass:** Database instance class for RDS.
- **DBName:** The name of the RDS database.
- **DBUser:** Database admin account username.
- **DBPassword:** Database admin account password.
- **MultiAZDatabase:** Whether to create a Multi-AZ MySQL Amazon RDS database instance.
- **WebServerCapacity:** Initial number of web server instances.
- **DBAllocatedStorage:** Size of the database in GB.

## Resources
1. **WebServerSecurityGroup:** Security group for the web server allowing access on ports 80 (HTTP) and 22 (SSH).
2. **DBSecurityGroup:** Security group for the database allowing access on port 3306 (MySQL).
3. **DBInstance:** RDS database instance configuration.
4. **MyDBSubnetGroup:** Database subnet group configuration.
5. **WebServerGroup:** Auto Scaling group for the web server instances.
6. **ELB:** Elastic Load Balancer configuration with listeners, health check settings, and security groups.
7. **LaunchConfig:** Launch configuration for EC2 instances in the Auto Scaling group.

## Outputs
- **WebsiteURL:** URL of the website using ELB DNS.
- **RDSEndpoint:** Endpoint of the RDS instance.

## Description
The CloudFormation script creates an environment with a scalable web application using EC2 instances, an RDS database, and an ELB for distributing traffic. Parameters allow customization of VPC, subnets, key pair, instance types, and database settings. The outputs provide the website URL and RDS endpoint for easy access.

Note: The `ImageId` in the `LaunchConfig` resource is set to an Amazon Machine Image (AMI) ID. You may need to replace it with the appropriate AMI for your region and requirements.


## WordPress

https://dev.to/aws-builders/create-a-wordpress-site-using-ec2-and-amazon-rds-45n

## PHP

https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_Tutorials.WebServerDB.CreateWebServer.html

# Command:


```bash
ssh -i /path/to/key.pem ec2-user@your-ec2-public-dns

## Install the Apache Web Server with PHP
sudo dnf update -y
sudo dnf install -y httpd php php-mysqli mariadb105
sudo systemctl start httpd
sudo systemctl enable httpd

## Set File Permissions for Apache Web Server
sudo usermod -a -G apache ec2-user
exit
groups
sudo chown -R ec2-user:apache /var/www
sudo chmod 2775 /var/www
find /var/www -type d -exec sudo chmod 2775 {} \;
find /var/www -type f -exec sudo chmod 0664 {} \;
cd /var/www
mkdir inc
cd inc

## Create dbinfo.inc File
<?php

define('DB_SERVER', 'db_instance_endpoint');
define('DB_USERNAME', 'tutorial_user');
define('DB_PASSWORD', 'master password');
define('DB_DATABASE', 'sample');
?>

## Create SamplePage.php File

cd /var/www/html
>SamplePage.php
nano SamplePage.php
--
<?php include "../inc/dbinfo.inc"; ?>
<html>
<body>
<h1>Sample page</h1>
<?php

  /* Connect to MySQL and select the database. */
  $connection = mysqli_connect(DB_SERVER, DB_USERNAME, DB_PASSWORD);

  if (mysqli_connect_errno()) echo "Failed to connect to MySQL: " . mysqli_connect_error();

  $database = mysqli_select_db($connection, DB_DATABASE);

  /* Ensure that the EMPLOYEES table exists. */
  VerifyEmployeesTable($connection, DB_DATABASE);

  /* If input fields are populated, add a row to the EMPLOYEES table. */
  $employee_name = htmlentities($_POST['NAME']);
  $employee_address = htmlentities($_POST['ADDRESS']);

  if (strlen($employee_name) || strlen($employee_address)) {
    AddEmployee($connection, $employee_name, $employee_address);
  }
?>
<!-- Input form -->
<!-- ... (rest of the PHP code) ... -->
</html>
