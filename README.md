# Project-6
Linux Server Configuration. This is the sixth and last project for Udacity FSND. You can visit the hosted server at http://34.232.210.119

# About
IP address: http://34.232.210.119
Port: 2200

## Get your server
1. Start a new Ubuntu Linux server instance on Amazon Lightsail. 
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

