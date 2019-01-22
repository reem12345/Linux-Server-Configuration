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

2. Change the SSH port from 22 to 2200. Make sure to configure the Lightsail firewall to allow it.

- edit sudo nano /etc/ssh/sshd_config file by change the port number from 22 to 2200

3. Configure the Uncomplicated Firewall (UFW) to only allow incoming connections for SSH (port 2200), HTTP (port 80), and NTP (port 123).

$ sudo ufw status it should be inactive.

$ sudo ufw default deny incoming

$ sudo ufw default allow outgoing

Allow incoming tcp packets on port 2200 $ sudo ufw allow 2200/tcp

Allow incoming udp packets on port 123 $ sudo ufw allow 123/udp

Allow HTTP traffic in : $ sudo ufw allow www

Deny tcp and udp packets on port 22 $ sudo ufw deny 22

unable the firewell: $ sudo ufw enable

close the connection using : ctrl + D

go to your instance choices in Amazon Lightsail and select manage and add:

HTTP , TCP , 80

CUSTOM , TCP, 2200

CUSTOM , UDP , 123

# Give grader access

1. create new user using this command: $ sudo adduser grader

2. Give grader the permission to sudo:

- in your terminal:  type $ sudo visudo

- add this line (grader  ALL=(ALL:ALL) ALL) after (root    ALL=(ALL:ALL) ALL) in the opend file.

3.Create an SSH key pair for grader using the ssh-keygen tool:

- From a new terminal run the command: $ ssh-keygen -f ~/.ssh/reem.rsa

- $ cat ~/.ssh/reem.rsa.pub (copy it's content)

- login as grader user and $ cd /home/grader then type this commands $ mkdir .ssh ,$ touch .ssh/authorized_keys and edit the authorized_keys file by paste the content of reem.rsa.pub using $ nano .ssh/authorized_keys command.

- change the authorized_keys permission:

$ sudo chmod 700 /home/grader/.ssh
$ sudo chmod 644 /home/grader/.ssh/authorized_keys 

- set /etc/ssh/sshd_config file for PasswordAuthentication and set it to no and change PermitRootLogin to no.

- Restart SSH: sudo service ssh restart

- close the connection and coonect to the server again using: ssh -i ~/.ssh/reem.rsa -p 2200 grader@18.197.52.126

# Prepare to deploy your project

1.Configure the local timezone to UTC.

2. Install and configure Apache to serve a Python mod_wsgi application.
install Apache: $ sudo apt-get install apache2.
install python3: $ sudo apt-get install libapache2-mod-wsgi-py3.
install Enable mod_wsgi $ sudo a2enmod wsgi
3.Install and configure PostgreSQL:

4. Create a new database user named catalog that has limited permissions to your catalog application database.

# Deploy the Item Catalog project

