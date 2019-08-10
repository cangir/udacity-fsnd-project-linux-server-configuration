# Udacity FSND Project: Linux Server Configuration
An Udacity Full Stack Nanodegree Project which describes how to deploy a Flask application


## Project Overview
This tutorial guides the steps to take a baseline installation of a Linux server and prepare it to host web applications and secure the server from a number of attack vectors, install and configure a database server, and deploy one of existing Flask-based Web applications onto it.

In this tutorial, a digitalocean droplet has been used.

### Technical Details
- Server IP Address: 68.183.11.197
- SSH server access port: 2200
- SSH login username: grader
- Application URL: https://statixdesign.com
- User Grader password: password
- Python Version: 3.7.3
- Pip3 Version: 18.1


## Setup the Server

### 1. Create the RSA Key Pair
First of all, you have to set up the public and private key pair on your local machine in order to authenticate yourself while securely logging in to the server via SSH. The private key stays in the local machine, and the public key in the server.

Run the following command:

```$ ssh-keygen```

Terminal will prompt to enter a passphrase. Pass phrase can be left empty but it is important for adding an additional layer of security to prevent unauthorized users from logging in. You will see something like this on terminal:
```
Generating public/private rsa key pair.
Enter file in which to save the key (/Users/cng/.ssh/id_rsa): udacity
Enter passphrase (empty for no passphrase): 
Enter same passphrase again: 
Your identification has been saved in /Users/cng/.ssh/udacity.
Your public key has been saved in /Users/cng/.ssh/udacity.pub.
The key fingerprint is:
SHA256:uZdjQjgxpLXdhRjfNcWGvpP57n1v649mxdWKu8ePXHA cng@Ahmets-MacBook-Pro.local
The key's randomart image is:
+---[RSA 2048]----+
|      o .o .. o+.|
|     + o.o.o ...o|
|    . + . o .. ..|
|       + .    . o|
|      o S    ..*E|
|       o . .. *oo|
|        o =  o +.|
|         + ...=+=|
|            .==B%|
+----[SHA256]-----+
```

The key pair is stored inside the ```~/.ssh/``` directory. 
- The public key is: udacity.pub
- The private key is: udacity

### 2. Setup DigitalOcean Droplet
1. Create an account on DigitalOCean or log in if you already have one.
2. Go to dashboard and click **Create Droplet**
3. Choose an image form the given list. I recommend choosing **Ubuntu 19.04 x64** as I will use it in this guide.
4. Choose your preffered size of droplet. My choice is **1GB/1 vCPU/2GB** configuration. This is the littlest choice but it can be extended.
5. Open **Add Your SSH Keys** section and paste the content of your public key, ```udacity.pub```, which you created in section 1. By the way, ```~/.ssh/authorized_keys```file will be automatically created with necessary permissions. Also ```PasswordAuthentication no``` rule will be add in the ```/etc/ssh/sshd_config``` file,  which disables password authentication on the **root** user and enforces SSH logins only.
6. Finally click **Create** to create the droplet and make yourself a coffee. It will take a few minutes. Once the droplet successfully created, a public address  will be assigned. 

### 3. Log in as root via SSH
Once the droplet is successfully created, you will be given a public IP address. In your lacal machine, run the following command in order to log into the server as **root* .

```ssh root@68.183.11.197```

This code will look for the private key in your local machine and log you in automatically. If the appropriate public key is on your server you will be logged in to your droplet as root user. 

```
Welcome to Ubuntu 19.04 (GNU/Linux 5.0.0-23-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  System information as of Wed Aug  7 09:34:43 UTC 2019

  System load:  0.0               Processes:           124
  Usage of /:   9.3% of 24.06GB   Users logged in:     0
  Memory usage: 36%               IP address for ens3: 68.183.11.197
  Swap usage:   0%

0 updates can be installed immediately.
0 of these updates are security updates.


Last login: Tue Aug  6 21:00:52 2019 from 79.193.52.74
root@ubuntu-s-1vcpu-1gb-ams3-01:~$ 
```

### 4. Update the System
Run the following command to update all the packages of the virtual server:

```sudo apt update && apt upgrade```

### 5. Change the SSH Port from 22 to 2200
1. Run following command to open sshd_config file:

```sudo nano /etc/ssh/sshd_config```

2. Find the line ```#Port 22``` and change it to ```Port 2200```, and save the file.

3. Restart the SSH server in order to be sure whether cahnges have come into effect:
```service ssh restart```

4. Finally run ```exit``` command and you will be logged out from your droplet to your local machine.

5. You can now log in to the server as root on port 2200. The -p option tells at what port the SSH server operates on.

```ssh root@68.183.11.197 -p 2200```

### 6. Configure Timezone to Use UTC
1. Run the following command:
```sudo dpkg-reconfigure tzdata```
2. Choose **None of the Above** and press Enter. 
3. Choose **UTC** and press Enter. and you will see something like this:
```
Current default time zone: 'Etc/UTC'
Local time is now:      Wed Aug  7 18:47:01 UTC 2019.
Universal Time is now:  Wed Aug  7 18:47:01 UTC 2019.
```

### 7. Setup the Firewall
1. Allow incoming connections for SSH (port 2200):
```ufw allow 2200/tcp```
2. Allow incoming connections for HTTP (port 80):
```ufw allow 80/tcp```
3. Allow incoming connections for NTP (port 123):
```ufw allow 123/udp```
4. Enable the above firewall rules:
```ufw enable```
5. Confirm whether the above rules have
been successfully applied or not:
```ufw status```

The output will be like this:
```
Status: active

To                         Action      From
--                         ------      ----
2200/tcp                   ALLOW       Anywhere                  
80/tcp                     ALLOW       Anywhere                  
123/udp                    ALLOW       Anywhere                  
2200/tcp (v6)              ALLOW       Anywhere (v6)             
80/tcp (v6)                ALLOW       Anywhere (v6)             
123/udp (v6)               ALLOW       Anywhere (v6)  
```

### 8. Create the User "Grader"
1. Run the following command:
```
sudo adduser grader
```

You will be asked to specify a password and details for new user. You can simply enter in order to leave empty but it is recommended not to leave password empty.

```
  Adding user `grader' ...
  Adding new group `grader' (1000) ...
  Adding new user `grader' (1000) with group `grader' ...
  Creating home directory `/home/grader' ...
  Copying files from `/etc/skel' ...
  Enter new UNIX password:
  Retype new UNIX password:
  passwd: password updated successfully
  Changing the user information for grader
  Enter the new value, or press ENTER for the default
	  Full Name []: Grader
	  Room Number []:
	  Work Phone []:
	  Home Phone []:
	  Other []:
  Is the information correct? [Y/n]
  ```
  
  2. Run the following command to add the user grader to the sudo group to grant it administrative access:
  ```
  usermod -aG sudo grader
  ```

### 9. Add SSH Access to the user grader
In order to allow SSH access to the user grader, log into the account of the user grader from your virtual server:

```sudo su - grader```
Then create **authorized_keys** file:
```
mkdir .ssh
chmod 700 .ssh
cd .ssh/
touch authorized_keys
chmod 644 authorized_keys
```

Then enter the following commands to allow SSH access to the user grader:
```
sudo nano authorized_keys
```

Inside nano, paste the contents of the public key file ```~/.ssh/udacity.pub``` and save. After save run ```exit```.

After running exit command, you will be returned to your local machine. Now you should be able to login as **grader** user with running following command in your local machine.
```
ssh grader@68.183.11.197 -p 2200
```

Please note that, you should change the given IP address with your droplet's IP address.


### 10. Disable Root Login
1. Login as root in your server with following command:
```
ssh root@68.183.11.197 -p 2200
```
2. Open the file ```/etc/ssh/sshd_config```:
``` 
# nano /etc/ssh/sshd_config
```
3. Change the line ```PermitRootLogin yes```, with ```PermitRootLogin no```.
4. Restart the SSH server:
```
sudo service ssh restart
```
5. Terminate the connection:
```
exit
```

After ```exit``` command, you will be returned to your local machine. Run:
```
ssh root@68.183.11.197 -p 2200
```
If you see a **Permission denied (publickey)** message, then this step is succesfully completed.

### 11. Install Apache Server
1. Login as grader with following command:
```
ssh grader@68.183.11.197 -p 2200
```

2. Install Apache:
```
sudo apt update
sudo apt install apache2
```

3. Enter the URL http://68.183.11.197 in your web browser. If you see **Apache 2 Ubuntu Default Page** and **It Works**, then your installation in successfull.

### 12. Inbstall Pip3
1. Run to install Pip3:
```
sudo apt install python3-pip
```

2. Check if it is installed with follwing command:
```pip3 --version```

If you see installed Pip version then your installation in successfull.

### 13. Install and Configure PostgreSQL
1. Enable PostgreSQL Apt Repository

Start with the import of the GPG key for PostgreSQL packages.
```
sudo apt-get install wget ca-certificates
wget --quiet -O - https://www.postgresql.org/media/keys/ACCC4CF8.asc | sudo apt-key add -
```

Now add the repository to your system.
```
sudo sh -c 'echo "deb http://apt.postgresql.org/pub/repos/apt/ `lsb_release -cs`-pgdg main" >> /etc/apt/sources.list.d/pgdg.list'
```

2. Install PostgreSQL on Ubuntu
Now as we have added PostgreSQL official repository in our system, First we need to update the repository list. After that install Latest PostgreSQL Server in our Ubuntu system using following commands.
```
sudo apt-get update
sudo apt-get install postgresql postgresql-contrib
```

3. Connect to PostgreSQL
After installing the PostgreSQL database server, by default, it creates a user ‘postgres’ with role ‘postgres’. Also creates a system account with the same name ‘postgres’. So to connect to Postgres server, log in to your system as user postgres and connect database.
```
sudo su - postgres
psql
```

4. Create database and user:
```
CREATE DATABASE catalog;
CREATE USER catalog;
ALTER ROLE catalog WITH PASSWORD 'password';
postgres=# GRANT ALL PRIVILEGES ON DATABASE catalog TO catalog;
```

5. To disconnect from PostgreSQL database command prompt just type below command and press enter. It will return you back to the Ubuntu command prompt.
```
\q
```



### 14. Setting Up Apache to Run the Flask Application
1. Install mod_wsgi
```
sudo apt install libapache2-mod-wsgi-py3
```
After installation, restart Apache server:
```
sudo service apache2 restart
```
 
2. Change the current working directory to ```/var/www/```:
```
cd /var/www
```

3. Clone your GitHub repository of Item Catalog application:
```
sudo git clone https://github.com/cangir/theme-catalog.git FlaskApp
```

4. Change the current directory to cloned repository.
```
cd FlaskApp/
```

5. Install requirements
```
sudo pip3 install -r requirements.txt
```

6. In order to setup virtual host configuration, run following command:
```
sudo nano /etc/apache2/sites-available/FlaskApp.conf
```

And add the following lines:
```
<VirtualHost *:80>
   ServerName www.yourdomain.tld
   ServerAlias yourdomain.tld *.yourdomain.tld
   ServerAdmin youremail@domain.tld
   WSGIScriptAlias / /var/www/FlaskApp/flaskapp.wsgi

   <Directory /var/www/FlaskApp/>
       Require all granted
   </Directory>
   Alias /static /var/www/FlaskApp/app/static
   <Directory /var/www/FlaskApp/app/static/>
       Require all granted
   </Directory>
   ErrorLog ${APACHE_LOG_DIR}/error.log
   LogLevel warn
   CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
```

**Note:** Change the ServerName and ServerAlias with your domain. If you do not have a domain just write your droplet's IP address for either area.

7. Enable the virtual host.
```
sudo a2ensite FlaskApp
```

8. Restart Apache server:
```
sudo service apache2 restart
```

9. Create the wsgi file.
Apache uses the .wsgi file to serve the Flask app. Move to the /var/www/FlaskApp/ directory and create a file named flaskapp.wsgi with following commands:
```
cd /var/www/FlaskApp/
sudo nano flaskapp.wsgi
```

Add the following lines to the **flaskapp.wsgi** file:
```
import sys
import logging
logging.basicConfig(stream=sys.stderr)
sys.path.insert(0, "/var/www/FlaskApp/")
sys.path.append("/home/grader/.local/lib/python3.7/site-packages")

from manage import app as application
```

In the above code, replace **manage** with the name of the main module of your application.

10. Restart Apache server:
```
sudo service apache2 restart
```

11. Disable default Apache page:
```
sudo a2dissite 000-default.conf
```

10. Restart Apache server:
```
sudo service apache2 restart
```

Now you can probably see your running application with your borowser.
Open: http://68.183.11.197 or http://yourdomain.tld


## Resources
1. [Initial Server Setup with Ubuntu 18.04](https://www.digitalocean.com/community/tutorials/initial-server-setup-with-ubuntu-18-04)
2. [How To Deploy a Flask Application on an Ubuntu VPS](https://www.digitalocean.com/community/tutorials/how-to-deploy-a-flask-application-on-an-ubuntu-vps)
3. [How to Install PostgreSQL 11 on Ubuntu 18.04 & 16.04 LTS](https://tecadmin.net/install-postgresql-server-on-ubuntu/)