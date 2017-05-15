# Linux-Server-Configuration
This project is a part of Full Stack Web Developer Nanodegree provided by Udacity.
I have used Amazon Lightsail for this project. If you prefer, you can use any other service that gives you a publicly accessible Ubuntu Linux server.

## Details about project ###
* You can see the project online using the following link:
    * http://ec2-54-237-241-86.compute-1.amazonaws.com/
* Public IP:
    * 54.237.241.86
* SSH port number:
    2200
    
## Things you should do when you create your own server instance ##
* Log in to [Lightsail](https://lightsail.aws.amazon.com), in case you do not have an account first make your account.
* Create your Lightsail instance, it is a Linux server running on a virtual machine inside an Amazon datacenter.
* Choose an instance image Ubuntu.
* Choose your instance plan.
* Give your instance a hostname.
* Wait for it to start-up.
* When its running you are good to go.

#### Note that normal copy-paste will not work in this terminal following are the steps you need to follow when copying the contents of a file ####
* Inside your terminal press ctrl+alt+shift 
* Clipboard will be opened
* Normal copy paste operations can be performed there
* First of all copy your contents there
* Press ctrl+shift+alt again to close the clipboard
* Then right click on that place where you want to copy
* You are good to go

#### Saving and exiting from nano ####
* Nano is a text editor provided by ubuntu
* To save the file and exit in nano you need to :
    * press ctrl+x
    * press y (n if you do not want to save your content)
    * press enter

## Methods, Steps and commands used ##
###  Create User named grader using commands below ###
* `sudo adduser grader`
* Note that you can check and verify if the user has been created using finger command.

### Give permissions to grader ###
* `sudo visudo`
* Add the following line under the "#User privilege specification" tag after the line `root ALL=(ALL:ALL) ALL` 
    * `grader ALL=(ALL:ALL) ALL`  
* Save the file and continue
* Add the following line to `/etc/sudoers.d/grader` using `sudo nano /etc/sudoers.d/garder`:
   * `grader ALL=(ALL:ALL) ALL`
* Add the following line to /etc/sudoers.d/root using `sudo nano /etc/sudoers.d/root`
   * `root ALL=(ALL:ALL) ALL` 

### Update all currently installed packages using the following commands ###
* `sudo apt-get update`
* `sudo apt-get upgrade`

### Change the SSH port 22 to 2200 using the following commands
* `sudo nano /etc/ssh/sshd_config`
* Now add port 2200 below port 22 
    * Port 2200
* In this file change `PermitRootLogin prohibit-password` to `PermitRootLogin no` to disallow root login
* Change `PasswordAuthentication` from no to yes.
* Save the file and restart
* Restart the service 
    * `sudo service ssh reload`

### Create SSH keys and copy to server manually ###
* Generate SSH key pair with: `ssh-keygen` (public and private keys will be generated that you will be using in the upcomming steps)
* Save this file in your ssh directory `/home/ubuntu/.ssh/name_of_the_app_you_want_to_deploy` 
* You can even add a password incase your keygen file is at stake
* Change the SSH port number configuration in Amazon lightsail to 2200(You can find it in the networking tab)
* Login to your grader account using the following command:
    * `ssh -v grader@*Public-IP-Address* -p 2200`
* Create .ssh directory using the following command:
    * `sudo mkdir .ssh`
* Create a file to store keytouch using the following command:
    * `sudo touch .ssh/authorized_keys`
* Your public key will be stored in a file named `.ssh/authorized_keys` use the following command to access it:
    * Your public and private keys will be generated using ssh-keygen
    * Copy the contents of public key file and paste it in `.ssh/authorized_keys` using the following command:
    * `sudo nano .ssh/authorized_keys` 
    * Save the file and continue
* Set permissions for files: 
    * `sudo chmod 700 .ssh chmod 644 .ssh/authorized_keys`
* Change PasswordAuthentication from yes back to no. :
    * `sudo nano /etc/ssh/sshd_config`
    * Save the file and continue
* Login with key pair:
    * `ssh grader@Public-IP-Address* -p 2200 -i ~/.ssh/item-catalog-website`
 
### Configure the Uncomplicated Firewall (UFW) such that it will allow only incoming connections for SSH (port 2200), HTTP (port 80), and NTP (port 123) ###
* Check UFW status just make it sure that its inactive:
    * `sudo ufw status`
* Deny all incomings:
    * `sudo ufw default deny incoming`
* Allow outgoing:
    * `sudo ufw default allow outgoing`
* Allow SSH:
    * `sudo ufw allow ssh`
* Allow SSH on port 2200:
    * `sudo ufw allow 2200/tcp`
* Allow HTTP on port 80:
    * `sudo ufw allow 80/tcp`
* Allow NTP on port 123:
    * `sudo ufw allow 123/udp`
* Turn on firewall
    * `sudo ufw enable`
    
### Installing  and configuring Apache to serve a Python mod_wsgi application  ###
* Install apache2:
    * `sudo apt-get install apache2`
* Install mod_wsgi:
    * `sudo apt-get install libapache2-mod-wsgi`
* Configure Apache to handle requests using the WSGI module:
    * `sudo nano /etc/apache2/sites-enabled/000-default.conf`
* Add the following line just above </VirtualHost>:
    * `WSGIScriptAlias / /var/www/html/myapp.wsgi` 
* Save the file and continue
* Restart Apache:
    * `sudo apache2ctl restart`
    
### Installing git ###
* Install git and python dev:
    * `sudo apt-get install git`
* Install python-dev package:
    * `sudo apt-get install python-dev`
* enable wsgi:
    * `sudo a2enmod wsgi`
* Clone any of your project(earlier nanodegree projects or anything else, I have used my item-catalog-website project)  which you will be using as a part of this project.
* Make sure that  it functions correctly when visiting your server’s IP address in a browser

### Create flask app taken from digitalocean ###
* `cd/var/www`
* `sudo mkdir catalog`
* `cd catalog`
* `sudo mkdir catalog`
* `cd catalog`
* `sudo mkdir static templates`
* `sudo nano __init__.py`
    * Make the following changes in `__init__.py`
    
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
* `sudo apt-get install python-pip`
* `sudo pip install virtualenv`
* `sudo virtualenv venv`
* `sudo chmod -R 777 venv`
* `sudo source venv/bin/activate`
* `sudo pip install Flask`
* `python __init__.py`
* `deactivate`

### Configure new host ###
* Create host config file:
    * `sudo nano /etc/apache2/sites-available/catalog.conf`
* paste this in that file:
```javascript
  <VirtualHost *:80>
  ServerName 54.237.241.86
  ServerAdmin admin@54.237.241.86
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

* Save and continue
* Enable `sudo a2ensite catalog`

### Clone your Github Repository using the following command ###
* Sudo git clone https://github.com/username/repositorname example i used:
    * `sudo git clone https://gurpreetkaurmalhotra/item-catalog-website`
* Move files from clone directory to catalog using the following command:
    * `sudo mv /var/www/catalog/item-catalog-website/* /var/www/catalog/catalog/`
* Remove clone directory:
    * `sudo rm -r item-catalog-website`
#### make .git inaccessible ####
* Change your directory to /var/www/catalog 
* Create a file named .htaccess:
    * `sudo nano .htaccess`
    * Paste the following line in that file:
        * `RedirectMatch 404 /\.git`
    * Save file and continue
    
### install the dependencies ###
* Install all relevant packages , following are the packages installed by me:
* Activate virtual environment:
    * `sudo source venv/bin/activate`
* Install httplib2:
    * `sudo pip install httplib2`
* Install requests:
    * `sudo pip install requests`
* Upgrade oauth2client:
    * `sudo pip install --upgrade oauth2client`
* Install sqlalchemy:
    * `sudo pip install sqlalchemy`
* Install flask sqlalchemy:
    * `sudo pip install Flask-SQLAlchemy`
* Install python-psycopg2:
    * `sudo pip install python-psycopg2`

### Install PostgreSQL ###
* Install postgresql
    * `sudo apt-get install postgresql`
* Install additional models:
    * `sudo apt-get install postgresql-contrib`
* Since I am using my item-catalog project I first configured my database_setup.py:
    * `sudo nano database_setup.py`
    * Made the following changes there:
     `engine = create_engine('postgresql://catalog:db-password@localhost/catalog')`
* I repeated the same for project.py
* Then I copied my project.py file into the `__init__.py` file:
    * `mv project.py __init__.py`
* Make catalog user:
    * `sudo adduser catalog`
* Enter postgrespsql
    * `postgresql`
* Create user catalog:
    * `CREATE USER catalog WITH PASSWORD 'db-password';`
* Change role of user catalog to:
    * `ALTER USER catalog CREATEDB;`
* Create new DB "catalog" with own of catalog:
    * `CREATE DATABASE catalog WITH OWNER catalog;`
* Connect to database\c catalog
* Revoke all rights:
    * `REVOKE ALL ON SCHEMA public FROM public;`
* Give accessto only catalog role
    * `GRANT ALL ON SCHEMA public TO catalog;`
* Quit postgres
* Exit
* Setup your database schema:
    * `python database_setup.py`

### fix OAuth to work with hosted Application ###
* Go to http://www.hcidata.info/host2ip.cgi 
* Get your host name by entering your public IP address which was given initially.
* Open apache config file:
    * `sudo nano /etc/apache2/sites-available/catalog.conf`
* Below the ServerAdmin paste:
    * ServerAlias your_host_name
* Make sure the virtual host is enabled:
    * `sudo a2ensite catalog`
* Restart apache server:
    * `sudo service apache2 restart`
 
 ### Adding hostname to google developer console(Since i used google sign up in my project) ###
* Go to google developer console
* Add your host name and IP address to Authorized Javascript origins.
* Add YOURHOSTNAME/ouath2callback to the Authorized redirect URIs.

#### At the end disable UFW port 22:
*` UFW deny 22`

#### Now you are good to go see your project live at the link provided by  http://www.hcidata.info/host2ip.cgi  ###


