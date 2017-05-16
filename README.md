# Linux Server Configuration

#### _Project 7 under the Full Stack Web Developer Nanodegree at Udacity_


#### _See project live at: http://ec2-54-80-95-79.compute-1.amazonaws.com_

***

#### For help using copying and pasting in terminal, saving and other errors faced see the Troubleshoot section at the end. Also don't forget to replace the Public IP Address with your own Public IP Address.

***

### Notes for reviewer:

* Public IP: 54.80.95.79
* SSH PORT: 2200
* Full project URL: http://ec2-54-80-95-79.compute-1.amazonaws.com

***

## Tasks given and method for completion:

### 1. Start a new Ubuntu Linux server instance on Amazon Lightsail:

  * Launch [Amazon Lightsail terminal](https://lightsail.aws.amazon.com/)
  * Must be logged into your Amazon Web Services account
  * Visit this link and press Create new instance of Ubuntu
  * You will get your respective public IP address
  * Download the default key-pair and copy to `/.ssh folder`
  * Open your terminal and run the command: `chmod 600 ~/.ssh/key.pem`
  * Now Use the command `ssh -i ~/.ssh/key.pem ubuntu@54.80.95.79` to create the instance on your terminal

### 2. Create a new user named grader:

  * Command: `sudo adduser grader`
    * Optional: Install finger to check user has been added using the following commands:
      * `sudo apt-get install finger`
      * `finger grader`

### 3. Give the grader the permission to sudo:

  * Run the command: `sudo visudo`
  * Inside the file add the following line below the root user under "#User privilege specification":
    * `grader ALL=(ALL:ALL) ALL`
  * Save file
  * Add grader to `/etc/sudoers.d/` by command: `sudo nano /etc/sudoers.d/grader` and add the following line to it:
    * `grader ALL=(ALL:ALL) ALL`
  * Add root to `/etc/sudoers.d/` by command: `sudo nano /etc/sudoers.d/root` and add the following line to it:
    * `root ALL=(ALL:ALL) ALL`

### 4. Update all currently installed packages:

  * List all the packages to update: `sudo apt-get update`
  * Update the packages: `sudo apt-get upgrade`
  * Further updates: `sudo apt-get dist-upgrade`
  * Other updates:
    * `sudo apt-get install aptitude`
    * `sudo aptitude update`
    * `sudo aptitude upgrade`

### 5. Change the SSH port from 22 to 2200 and other SSH configuration required from grading rubic:

  * Run the command: `nano /etc/ssh/sshd_config` add `Port 2200` below `Port 22`
  * While in the file also change `PermitRootLogin prohibit-password` to `PermitRootLogin no` to disallow root login
  * Change `PasswordAuthentication` from no to yes. We will change back after finishing SHH login setup
  * Save file
  * Restart ssh service: `sudo service ssh reload`

### 6. Create SSH keys and copy to server manually:

  * Change the SSH port number configuration in Amazon lightsail in networking tab to 2200.
  * On your local machine generate SSH key pair with: `ssh-keygen`
  * Save your keygen file in your ssh directory `/home/ubuntu/.ssh/`
    * Example full file path that could be used: `/home/ubuntu/.ssh/item-catalog`
  * Enter a password for your keygen file(you will be prompted to enter this password when you connect with key pair)
  * On your local machine read contents of the public key `cat .ssh/item-catalog.pub` and copy its content.
  * Login into grader account using password set during user creation: `ssh -v grader@54.80.95.79 -p 2200`
  * Make .ssh directory: `mkdir .ssh`
  * Make file to store key: `touch .ssh/authorized_keys`
  * Paste the key you copied in the file you just created in grader: `sudo nano .ssh/authorized_keys`
  * Save file
  * Set permissions for files:
    * `chmod 700 .ssh`
    * `chmod 644 .ssh/authorized_keys`
  * Change `PasswordAuthentication` from yes back to no in sshd_config using: `nano /etc/ssh/sshd_config`
  * Save file
  * Login with key pair: `ssh grader@54.80.95.79 -p 2200 -i ~/.ssh/item-catalog`

### 7. Configure the Uncomplicated Firewall (UFW) to only allow incoming connections for SSH (port 2200), HTTP (port 80), and NTP (port 123):

  * Check UFW status to make sure its inactive: `sudo ufw status`
  * Deny all incoming by default: `sudo ufw default deny incoming`
  * Allow outgoing by default: `sudo ufw default allow outgoing`
  * Allow SSH: `sudo ufw allow ssh`
  * Allow SSH on port 2200: `sudo ufw allow 2200/tcp`
  * Allow HTTP on port 80: `sudo ufw allow 80/tcp`
  * Allow NTP on port 123: `sudo ufw allow 123/udp`
  * Turn on firewall: `sudo ufw enable`
  * Check ufw status: `sudo ufw status`
    This will show that UFW is now active with the settings below:
    
    ```
    To                         Action      From
    --                         ------      ----
    22                         ALLOW       Anywhere
    2200/tcp                   ALLOW       Anywhere
    80/tcp                     ALLOW       Anywhere
    123/udp                    ALLOW       Anywhere
    22 (v6)                    ALLOW       Anywhere (v6)
    2200/tcp (v6)              ALLOW       Anywhere (v6)
    80/tcp (v6)                ALLOW       Anywhere (v6)
    123/udp (v6)               ALLOW       Anywhere (v6)
    ```
    
### 8. Configure the local timezone to UTC:

  * Start the configuration process: `dpkg-reconfigure tzdata`
  * In the window that appears, use the arrow keys to Scroll to the bottom of the Continents list and select `Etc` or `None of the Above` and then in the second list, select `UTC`

### 9. Install and configure Apache to serve a Python mod_wsgi application:

  * Run the command: `sudo apt-get install apache2`
  * Install mod_wsgi: `sudo apt-get install libapache2-mod-wsgi`
  * Configure Apache to handle requests using the WSGI module: `sudo nano /etc/apache2/sites-enabled/000-default.conf`
    * add `WSGIScriptAlias / /var/www/html/myapp.wsgi` before `</VirtualHost>` closing line
  * Save file
  * Restart Apache: `sudo apache2ctl restart`

### 10. Install python dev and verify WSGI is enabled:

  * Install python-dev package: `sudo apt-get install python-dev`
  * Verify wsgi is enabled: `sudo a2enmod wsgi`
  * Create flask app taken from digitalocean using the following commands:

    * `cd /var/www`
    * `sudo mkdir catalog`
    * `cd catalog`
    * `sudo mkdir catalog`
    * `cd catalog`
    * `sudo mkdir static templates`
    * `sudo nano __init__.py`
    
      ```
      from flask import Flask
      app = Flask(__name__)
      @app.route("/")
      def hello():
        return "Hello, world (Testing!)"
      if __name__ == "__main__":
        app.run()
      ```

### 11. Install flask:

Run the following commands:
  * `sudo apt-get install python-pip`
  * `sudo pip install virtualenv`
  * `sudo virtualenv venv`
  * `sudo chmod -R 777 venv`
  * `source venv/bin/activate`
  * `pip install Flask`
  * `python __init__.py`
  * `deactivate`

### 12. Configure And Enable New Virtual Host:

  * Create host config file: `sudo nano /etc/apache2/sites-available/catalog.conf`
  * Paste the following:
    
    ```
    <VirtualHost *:80>
      ServerName 54.80.95.79
      ServerAdmin admin@54.80.95.79
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
  * Save file
  * Enable `sudo a2ensite catalog`
  * Create the wsgi file:
    * `cd /var/www/catalog`
    * Run `sudo nano catalog.wsgi` and paste the following content:

      ```
      #!/usr/bin/python
      import sys
      import logging
      logging.basicConfig(stream=sys.stderr)
      sys.path.insert(0,"/var/www/catalog/")

      from catalog import app as application
      application.secret_key = 'Add your secret key'
      ```
      * Save file

  * `sudo service apache2 restart`


### 13. Install git, clone and setup your Catalog App project (from your GitHub repository from earlier in the Nanodegree program) so that it functions correctly when visiting your server’s IP address in a browser. Remember to set this up appropriately so that your .git directory is not publicly accessible via a browser!

  * Install git: `sudo apt-get install git`
  * Clone directory: `sudo git clone https://github.com/sukhdevsinghsaggar/item-catalog`
  * Make sure you get hidden files in move: `shopt -s dotglob`
  * Move files from clone directory to catalog `mv /var/www/catalog/item-catalog/* /var/www/catalog/catalog/`
  * Remove clone directory: `sudo rm -r item-catalog`

### 14. Make .git inaccessible:

  * In `cd /var/www/catalog/` create .htaccess file: `sudo nano .htaccess`
  * Paste in it: `RedirectMatch 404 /\.git`
  * Save file

### 15. Install dependencies:

  Run the following commands:
  * `source venv/bin/activate`
  * `pip install httplib2`
  * `pip install requests`
  * `sudo pip install --upgrade oauth2client`
  * `sudo pip install sqlalchemy`
  * `pip install Flask-SQLAlchemy`
  * `sudo pip install python-psycopg2`
  
  If you used any other packages in your project be sure to install those as well.

### 16. Install and configure PostgreSQL:

  * Install postgres: `sudo apt-get install postgresql`
  * Install additional models: `sudo apt-get install postgresql-contrib`
  * By default no remote connections are not allowed
  * Config database_setup.py: `sudo nano database_setup.py`:
    * engine = create_engine('postgresql://catalog:db-password@localhost/catalog')
  * Repeat for project.py
  * Copy your main project.py file into the init.py file: `mv project.py __init__.py`
  * Add catalog user: `sudo adduser catalog`
  * Login as postgres super user: `sudo su - postgres`
  * Enter postgres: `psql`
  * Create user catalog: `CREATE USER catalog WITH PASSWORD 'db-password';`
  * Change role of user catalog to CREATEDB: `ALTER USER catalog CREATEDB;`
  * List all users and roles to verify: `\du`
  * Create new DB "catalog" with owner catalog: `CREATE DATABASE catalog WITH OWNER catalog;`
  * Connect to database: `\c catalog`
  * Revoke all rights: `REVOKE ALL ON SCHEMA public FROM public;`
  * Give access to only catalog role: `GRANT ALL ON SCHEMA public TO catalog;`
  * Quit postgres: `\q`
  * Logout from postgres super user: `exit`
  * Setup your database schema `python database_setup.py`

### 17. Fix OAuth to work with hosted Application:

  * Google wont allow the IP address to make redirects so we need to set up the host name address to be usable.
  * Go to http://www.hcidata.info/host2ip.cgi to get your host name by entering your public IP address of your instance.
  * Open apache config file: `sudo nano /etc/apache2/sites-available/catalog.conf`
  * Below the ServerAdmin paste: `ServerAlias YOURHOSTNAME`
  * Make sure the virtual host is enabled: `sudo a2ensite catalog`
  * Restart apache: `server sudo service apache2 restart`
  * In your google developer console add your host name and IP address to Authorized Javascript origins. And add YOURHOSTNAME/ouath2callback to the Authorized redirect URIs.
  
### 18. At the end disable UFW port 22:

  * `UFW deny 22`
  * Run the command: `nano /etc/ssh/sshd_config` and delete the `Port 22`

***

## Troubleshoot:

* #### Saving in Nano file editor:
  * Press Ctrl + X
  * Press Y (N if you do not want to save your content)
  * Press Enter
* #### Copy:
  * Open the file you want to copy
  * Select the content to copy and press Ctrl + C
  * Press Ctrl + Shift + Alt. New sidebar will open in which you will see your copied content under the heading clipboard
  * Copy the contents from there and paste it
* #### Paste:
  * Paste the content in the clipboard box after pressing Ctrl + Alt + Shift
  * Now open the file in which you want to paste the content and press the right mouse button
* #### If you get a No such file or directory error:
  * For example: I got this error for my 'client_secrets.json' file. I fixed it using a raw path to the file:  `open(r'/var/www/catalog/catalog/client_secrets.json', 'r').read())`
  * You'll also need to do this for any other instances of the file path(If you get the same error for them)
* #### If you get any error regarding any module or library used make sure that you have installed them in the virtual environment i.e after running the command `source venv/bin/activate` in grader
* #### If you get any other error just see the last line of the apache error log available at: `/var/log/apache2/error.log`

***

#### _Now you are good to go see your project live at the link provided by http://www.hcidata.info/host2ip.cgi_
