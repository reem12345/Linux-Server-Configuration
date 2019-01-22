# Linux-Server-Configuration
This project is linked to the Configuring Linux Web Servers course, which teaches you to secure and set up a Linux server. By the end of this project, you will have one of your web applications running live on a secure web server.
# information about item catalog website
public ip : 18.197.52.126

private ip: 172.26.7.193

port number: 2200

url websit: http://18.197.52.126.xip.io/

operting system: Ubuntu 16.04 LTS, Linux

server instance: Amazon Lightsail. server

Database: PostgreSQL database server.

# Project Steps:
# Get your server
1. Start a new Ubuntu Linux server instance on Amazon Lightsail https://lightsail.aws.amazon.com/

2. SSH into your server: 

- Download the ssh from your account in Amazon Lightsail in .ssh folder and rename it reem.pem

- change the mod of the peem.pem to chmod 600 ~/.ssh/reem.pem in your terminal

- connect to the server using this command ssh -i ~/.ssh/reem.pem ubuntu@18.197.52.126

# Secure your server
1. Update all currently installed packages

$ sudo apt-get update

$ sudo apt-get upgrade

$ sudo apt-get dist-upgrade

# Give grader access
# Prepare to deploy your project
# Deploy the Item Catalog project

