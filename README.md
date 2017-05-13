# Linux-Server-Configuration
This project is a part of Full Stack Web Developer Nanodegree provided by Udacity.
I have used Amazon Lightsail for this project. If you prefer, you can use any other service that gives you a publicly accessible Ubuntu Linux server.

## Details about project ###
* You can see the project online using the following link:
    * http://ec2-54-146-100-10.compute-1.amazonaws.com/
* Public IP:
    * 54.146.100.10
* SSH port number:
    2200

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
* Add the following line to /etc/sudoers.d/garder using:
    * sudo nano /etc/sudoers.d/garder
        * grader ALL=(ALL:ALL) ALLby command sudo nano /etc/sudoers.d/grader
* Add the following line to /etc/sudoers.d/root using:
    * sudo nano /etc/sudoers.d/root
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
    * cat .ssh/project5.pub
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
    from flask import Flask
    app = Flask(__name__)
    @app.route("/")
    def hello():
        return "Hello, Udacity"
    if __name__ == "__main__":
        app.run()
    ```
    
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
```javascript
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
```

* save and continue
* Enable sudo a2ensite catalog

### Clone your Github Repository using the following command ###
* sudo git clone https://github.com/username/repositorname
* Move files from clone directory to catalog using the following command:
    * mv /var/www/catalog/item-catalog/* /var/www/catalog/catalog/
* remove clone directory:
    * sudo rm -r devpost
#### make .git inaccessible ####
* Change your directory to /var/www/catalog 
* create a file named .htaccess:
    * sudo nano .htaccess
    * paste the following line in that file:
        * RedirectMatch 404 /\.git
    * save file and continue
    
### install the dependencies ###
* Install all relevant packages , following are the packages installed by me:
* Activate virtual environment:
    * source venv/bin/activate
* Install httplib2:
    * pip install httplib2
* Install requests:
    * pip install requests
* Upgrade oauth2client:
    * sudo pip install --upgrade oauth2client
* Install sqlalchemy:
    * sudo pip install sqlalchemy
* Install flask sqlalchemy:
    * pip install Flask-SQLAlchemy
* Install python-psycopg2:
    * sudo pip install python-psycopg2

### Install PostgreSQL ###
* Install postgresql
    * sudo apt-get install postgresql
* install additional models:
    * sudo apt-get install postgresql-contrib
* Since I am using my item-catalog project I first configured my database_setup.py:
    * sudo nano database_setup.py
    * made the following changes there:
     `python engine = create_engine('postgresql://catalog:db-password@localhost/catalog')`
* I repeated the same for project.py
* Then I copied my project.py file into the init.py file:
    * mv project.py __init__.py
* Make catalog user:
    * sudo adduser catalog
* enter postgrespsql
* Create user catalog:
    * CREATE USER catalog WITH PASSWORD 'db-password';
* Change role of user catalog to:
    * ALTER USER catalog CREATEDB;
Create new DB "catalog" with own of catalog:
    * CREATE DATABASE catalog WITH OWNER catalog;
* Connect to database\c catalog
* Revoke all rights:
    * REVOKE ALL ON SCHEMA public FROM public;
* Give accessto only catalog role
    * GRANT ALL ON SCHEMA public TO catalog;
* Quit postgres
* exit
* Setup your database schema python database_setup.py

### fix OAuth to work with hosted Application ###
* go to http://www.hcidata.info/host2ip.cgi 
* get your host name by entering your public IP address which was given initially.
* open apache configbfile:
    * sudo nano /etc/apache2/sites-available/catalog.conf
* below the ServerAdmin paste:
    * ServerAlias your_host_name
* make sure the virtual host is enabled:
    * sudo a2ensite catalog
* restart apache server:
    * sudo service apache2 restart
 
 ### Adding hostname to google developer console(Since i used google sign up in my project) ###
* Go to google developer console
* add your host name and IP address to Authorized Javascript origins.
* add YOURHOSTNAME/ouath2callback to the Authorized redirect URIs.

#### At the end disable UFW port 22:
* UFW deny 22

#### Now you are good to go see your project live at the link provided by  http://www.hcidata.info/host2ip.cgi  ###


