# Project-6
Linux Server Configuration. This is the sixth and last project for Udacity FSND. You can visit the hosted server at http://34.232.210.119

# About
IP address: http://34.232.210.119
Port: 2200

## Get your server
1. Start a new Ubuntu Linux server instance on Amazon Lightsail
2. Choose OS and then Ubuntu
3. Wait for it to start up

## Secure your server
1. Update all packages
```sh
sudo apt-get update
sudo apt-get upgrade
```
2. For auto upgrades
```sh
sudo dpkg-reconfigure --priority=low unattended-upgrades
```
3. Change the SSH port from 22 to 2200 (change 22 to 2200 in editing menu)
```sh
sudo nano /etc/ssh/sshd_config
```
4. Configure the Uncomplicated Firewall (UFW) to only allow incoming connections for SSH (port 2200), HTTP (port 80), and NTP (port 123)
```sh
sudo ufw default deny incoming
sudo ufw default allow outgoing
sudo ufw allow www
sudo ufw allow ntp
sudo ufw allow 2200/tcp
sudo ufw allow 80/tcp
sudo ufw allow 123/tcp
sudo ufw enable
```

## Give grader access
1. Create a new user account named grader
`sudo adduser grader`
2. Give grader the permission to use sudo. For this, run: `sudo nano /etc/sudoers.d/grader` and add this line:
`grader ALL=(ALL) NOPASSWD:ALL`.
3. Create an SSH key pair for grader using ssh-keygen
`ssh-keygen -t rsa`
(This key will be provided to grader in `Notes` section)

## Prepare to deploy your project
1. Configure the local timezone to UTC (choose None of the above then on next page -> choose UTC)
```sh
sudo dpkg-reconfigure tzdata
```
2. Install and configure Apache to serve a Python mod_wsgi application
```sh
sudo apt-get install apache2
sudo apt-get install libapache2-mod-wsgi
```
3.  Install and configure PostgreSQL
```sh
sudo apt-get install postgresql postgresql-contrib
```
- To ensure that remote connections to PostgreSQL are not allowed, do check that the configuration file `/etc/postgresql/9.3/main/pg_hba.conf` only allow connections from the local host addresses 127.0.0.1 for IPv4 and ::1 for IPv6
- Create a PostgreSQL user called `catalog` and database `catalog`
```sh
sudo -u postgres createuser -P catalog
sudo -u postgres createdb -O catalog catalog
```
4. Installing required python modules
```sh
sudo apt-get install python-psycopg2 python-flask
sudo apt-get install python-sqlalchemy python-pip
sudo pip install oauth2client
sudo pip install requests
sudo pip install httplib2
```
5. Install git and setup your Catalog project files
```sh
sudo apt-get install git
cd /var/www
sudo mkdir FlaskApp
cd FlaskApp```

## Deploy the Item Catalog project
```sh
git clone https://github.com/iTanuj/Project-4.git
sudo mv ./Project-4 ./FlaskApp
cd FlaskApp
sudo mv project.py __init__.py
```
In all .py files replace create_engine() as `create_engine('postgresql://catalog:password@localhost/catalog')`
```sh
sudo apt-get install python-pip
sudo python database_setup.py
sudo python insert_games.py
```
### Creating apache2 conf
Create apache2 config file for FlaskApp
`sudo nano /etc/apache2/sites-available/FlaskApp.conf`
and paste this:
```sh
<VirtualHost *:80>
	ServerName 34.232.210.119
	ServerAdmin tanuj.goodboy77@gmail.com
	WSGIScriptAlias / /var/www/FlaskApp/flaskapp.wsgi
	<Directory /var/www/FlaskApp/FlaskApp/>
		Order allow,deny
		Allow from all
	</Directory>
	Alias /static /var/www/FlaskApp/FlaskApp/static
	<Directory /var/www/FlaskApp/FlaskApp/static/>
		Order allow,deny
		Allow from all
	</Directory>
	ErrorLog ${APACHE_LOG_DIR}/error.log
	LogLevel warn
	CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
```
Enable this by `sudo a2ensite FlaskApp` after disabling default file by running `sudo a2dissite 000-default.conf`
Make Apache2 changes live: `sudo service apache reload`
### Creating .wsgi
Move to FlaskApp
```sh
cd /var/www/FlaskApp
sudo nano flaskapp.wsgi 
```
and paste this:
```sh
#!/usr/bin/python
import sys
import logging
logging.basicConfig(stream=sys.stderr)
sys.path.insert(0,"/var/www/FlaskApp/")

from FlaskApp import app as application
application.secret_key = '<secret_key>'
```
### Restart Apache
`sudo service apache2 restart`
