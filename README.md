# Udacity FSND Project: Linux Server Configuration
An Udacity Full Stack Nanodegree Project which describes how to deploy a Flask application


## Project Overview
This tutorial guides the steps to take a baseline installation of a Linux server and prepare it to host web applications and secure the server from a number of attack vectors, install and configure a database server, and deploy one of existing Flask-based Web applications onto it.

In this tutorial, digitalocean web servers has been used.

### Technical Details
- Server IP Address: 68.183.11.197
- SSH server access port: 2200
- SSH login username: grader
- Application URL: https://statixdesign.com

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