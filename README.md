# Linux-Server-Configuration
This project is a part of Full Stack Web Developer Nanodegree provided by Udacity.
I have used Amazon Lightsail for this project. If you prefer, you can use any other service that gives you a publicly accessible Ubuntu Linux server.

## Things you should do when you create your own server instance ##
* Log in to Lightsail, in case you do not have an account first make your account.
* Create your Lightsail instance, it is a Linux server running on a virtual machine inside an Amazon datacenter.
* Choose an instance image Ubuntu.
* Choose your instance plan.
* Give your instance a hostname.
* Wait for it to start-up.
* When its running you are good to go.

###  Create User named grader using commands below ###
* sudo adduser grader
* Note that you can check and verify if the user has been created using finger command.

### Give permissions to grader ###
* sudo visudo
* Add the following line under "#User privilege specification" just bellow the line root=(ALL:ALL) ALL 
 * ALL=(ALL:ALL) ALL  
* Save the file and continue
* Add the following line to /etc/sudoers.d/garder using sudo nano /etc/sudoers.d/garder
 * grader ALL=(ALL:ALL) ALLby command sudo nano /etc/sudoers.d/grader
* Add the following line to /etc/sudoers.d/root using sudo nano /etc/sudoers.d/root
 * root ALL=(ALL:ALL) 

### Update all currently installed packages using the following commands ###
* sudo apt-get update
* sudo apt-get upgrade

### Change the SSH port 22 to 2200 using the following commands
* nano /etc/ssh/sshd_config add port 2200 below port 22
* In this file change PermitRootLogin prohibit-password to PermitRootLogin no to disallow root login
* Change PasswordAuthentication from no to yes.
* save the file and restart
* restart the service 
 * ssh servicesudo service ssh reload

### Create SSH keys and copy to server manually ###
* Generate SSH key pair with: ssh-keygen
* save this file in your ssh directory /home/ubuntu/.ssh/name_of_the_app_you_want_to_deploy 
* You can even add a password incase your keygen file is at stake
* Change the SSH port number configuration in Amazon lightsail to 2200(You can find it in the networking tab)
* login to your grader account using the following command:
 * ssh -v grader@*Public-IP-Address* -p 2200
* Create .ssh directory using the following command:
 * mkdir .ssh
* Create a file to store keytouch using the following command:
 * touch .ssh/authorized_keys
* Your public key will be stored in a file named .ssh/authorized_keys usethe following command to access it:
 *  cat .ssh/project5.pub
 * Copy the contents of this file and paste it in .ssh/authorized_keys using the following command:
  * nano .ssh/authorized_keys paste contents
 * Save the file and continue
* Set permissions for files: 
 * chmod 700 .ssh chmod 644 .ssh/authorized_keys
* Change PasswordAuthentication from yes back to no. :
 * nano /etc/ssh/sshd_config
* Save the file and continue
* login with key pair:
 * ssh grader@Public-IP-Address* -p 2200 -i ~/.ssh/project5
 
### Configure the Uncomplicated Firewall (UFW) such that it will allow only incoming connections for SSH (port 2200), HTTP (port 80), and NTP (port 123) ###
* Check UFW status just make it sure that its inactive:
    * sudo ufw status
* Deny all incomings:
    * sudo ufw default deny incoming
* Allow outgoing:
    * sudo ufw default allow outgoing
* Allow SSH:
    * sudo ufw allow ssh
* Allow SSH on port 2200:
    * sudo ufw allow 2200/tcp
* Allow HTTP on port 80:
    * sudo ufw allow 80/tcp
* Allow NTP on port 123:
    * sudo ufw allow 123/udp
* Turn on firewall
    * sudo ufw enable
    
### Installing  and configuring Apache to serve a Python mod_wsgi application  ###
* Install apache2:
    * sudo apt-get install apache2
* install mod_wsgi:
    * sudo apt-get install libapache2-mod-wsgi
* configure Apache to handle requests using the WSGI module:
    * sudo nano /etc/apache2/sites-enabled/000-default.conf
* add WSGIScriptAlias:
    * / /var/www/html/myapp.wsgi before </VirtualHost> closing line
* save the file and continue
* Restart Apache:
    * sudo apache2ctl restart
    
### Installing git ###
* Install git and python dev:
    * sudo apt-get install git
* Install python-dev package:
    * sudo apt-get install python-dev
* enable wsgi:
    * sudo a2enmod wsgi
* Clone any of your project(earlier nanodegree projects or anything else, I have used my item-catalog-website project)  which you will be using as a part of this project.
* Make sure that  it functions correctly when visiting your server’s IP address in a browser

### Create flask app taken from digitalocean ###
* cd /var/www
* sudo mkdir catalog
* cd catalog
* sudo mkdir catalog
* cd catalog
* sudo mkdir static templates
* sudo nano __init__.py
    * Make the following changes in __init__.py
    ```javascript
         ```
    from flask import Flask
    app = Flask(__name__)
    @app.route("/")
    def hello():
        return "Hello, Udacity"
    if __name__ == "__main__":
        app.run()
* Note that we are doing this in order to see if we are on the right track

### Install Flask, set permissions and activate virtual environment ###
* sudo apt-get install python-pip
* sudo pip install virtualenv
* sudo virtualenv venv
* sudo chmod -R 777 venv
* source venv/bin/activate
* pip install Flask
* python __init__.py
* deactivate

### Configure new host ###
* Create host config file sudo nano /etc/apache2/sites-available/catalog.conf
* paste this in that file:
`code`
<VirtualHost *:80>
  ServerName 34.201.114.178
  ServerAdmin admin@34.201.114.178
  WSGIScriptAlias / /var/www/catalog/catalog.wsgi
  <Directory /var/www/catalog/catalog/>
      Order allow,deny
      Allow from all
  </Directory>
  Alias /static /var/www/catalog/catalog/static
  <Directory /var/www/catalog/catalog/static/>
      Order allow,deny
      Allow from all
  </Directory>
  ErrorLog ${APACHE_LOG_DIR}/error.log
  LogLevel warn
  CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
* save and continue
* Enable `sudo a2ensite catalog`

