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

$ sudo apt-get install postgresql, while logged in as grader.

- Switch to the postgres user: sudo su - postgres.

- Open PostgreSQL interactive terminal with psql.

- Create the catalog user with a password and give them the ability to create databases:

postgres=# CREATE ROLE catalog WITH LOGIN PASSWORD 'catalog';

postgres=# ALTER ROLE catalog CREATEDB;

Exit psql: \q, then get back to grader user via exit.

- Create a new Linux user called catalog: sudo adduser catalog. Enter password and fill out information.

- Give to catalog user the permission to sudo. Run: sudo visudo.

look for the line that have

root    ALL=(ALL:ALL) ALL

grader  ALL=(ALL:ALL) ALL

Below this line, add a new line to give sudo permissions to catalog user. the file should look like this:


(root    ALL=(ALL:ALL) ALL

grader  ALL=(ALL:ALL) ALL

catalog  ALL=(ALL:ALL) ALL)



- while logged in as catalog.
create database via : createdb catalog.
Run psql then \l to check that the new database has been created successfully.

Exit psql: \q, then get back to grader user via exit.
4. Install git.
- While logged in as grader.
install git via : sudo apt-get install git.

# Deploy the Item Catalog project
- Clone and setup Item Catalog project from Github repository created earlier in the Nanodegree program.
While logged in as grader.

- create /var/www/catalog/ directory.

- cd to that directory and clone item catalog repository.

- sudo git clone https://github.com/reem12345/item_catalog.git

- From the /var/www directory, change the ownership of the catalog directory to grader via: sudo chown -R grader:grader catalog/

- Cd to the /var/www/catalog/item_catalog directory.

- Rename the application.py file to __init__.py using: mv application.py __init__.py.

- In __init__.py replace this line:
# app.run(host='0.0.0.0', port=5000)
with app.run()

In database_setup.py, replace this line:
# engine = create_engine('sqlite:///catalogDB.db')
with: 
engine = create_engine('postgresql://catalog:catalog@localhost/catalog')

- authenticating login through google

From Google Cloud Plateform.

Click APIs & services on left menu then Credentials.

edit your an OAuth Client ID, add and add http://18.197.52.126.xip.io/ as authorized JavaScript origins.

Add http://18.197.52.126.xip.io/categories/ as authorized redirect URI.

Download the corresponding JSON file, open it then copy the contents and paste them into /var/www/catalog/item_catalog/c.json.


Add the path of c.json to  __init__.py file

- Install virtual environment and all the project dependencies

- While logged in as grader.

install pip: sudo apt-get install python3-pip.

Install the virtual environment: sudo apt-get install python-virtualenv

Change to the /var/www/catalog/catalog/ directory.

Create the virtual environment: sudo virtualenv -p python3 venv3.

Change the ownership to grader with: sudo chown -R grader:grader venv3/.

Activate the new environment: . venv3/bin/activate.

Install the following dependencies:


pip install flask
pip install sqlalchemy
pip install requests
pip install psycopg2
pip install --upgrade oauth2client
Run python3 __init__.py to check everything is okay:

* Running on http://127.0.0.1:5000/ (Press CTRL+C to quit)

Deactivate the virtual environment: deactivate.

15- set up virtual host


Create /etc/apache2/sites-available/catalog.conf and add the following lines to configure the virtual host:

<VirtualHost *:80>
   
   ServerName 35.159.41.242
   
   ServerAlias 35.159.41.242.xip.io
   
   WSGIScriptAlias / /var/www/catalog/item_catalog.wsgi
   
   <Directory /var/www/catalog/item_catalog/>
       
       Order allow,deny
         
         Allow from all
   
   </Directory>
   
   Alias /static /var/www/catalog/item_catalog/static
   
   <Directory /var/www/catalog/item_catalog/static/>
         
         Order allow,deny
         
         Allow from all
   
   </Directory>
   
   ErrorLog ${APACHE_LOG_DIR}/error.log
   
   LogLevel warn
   
   CustomLog ${APACHE_LOG_DIR}/access.log combined

</VirtualHost>

Add the following line in /etc/apache2/mods-enabled/wsgi.conf file to use Python 3.
       
       WSGIPythonPath /var/www/catalog/item_catalog/venv3/lib/python3.5/site-packages

sudo a2ensite catalog to enable virtual host.

sudo service apache2 reload to reload Apache server.

- Setting up flask application

Create /var/www/catalog/item_catalog.wsgifile then add these lines

activate_this = '/var/www/catalog/item_catalog/venv3/bin/activate_this.py'

with open(activate_this) as file_:
   
   exec(file_.read(), dict(__file__=activate_this))

#!/usr/bin/python3

import sys

import logging

logging.basicConfig(stream=sys.stderr)

sys.path.insert(0, "/var/www/catalog/item_catalog/")

sys.path.insert(1, "/var/www/catalog/")

from catalog import app as application

application.secret_key = "---"


- sudo service apache2 restart to restart the Apache server.

- set up the database and fill it with data

cd to /var/www/catalog/item_catalog/ directory.

activate the virtual environment via :. venv3/bin/activate.

Run database_setup.py first

Run database_information.py second

deactivate virtual environment.
- start the web application

sudo a2dissite 000-default.conf to disable the default Apache site.

sudo service apache2 reload

sudo service apache2 restart.

open in browser http://18.197.52.126.xip.io

- update the pakages

$ sudo apt-get update
$ sudo apt-get upgrade

$ sudo apt-get dist-upgrade
