											#LINUXCONFIG

###             Fifth project as part of the Udacity/Google - Full Stack Web Developer - Nanodegree
 
###Key things learnt:
1. Configuring ssh access(sshd_config file) to a Amazon EC2 server via password as well as via public/private key.
2. Creating user and configuring the access permissions of the user.
3. Configuring ports and firewall.
4. Installing and using apache, python mod_wsgi, postgres and git. Using virtualenv. 
5. Learnt how various modules like apache, mod_wsgi, interacted with each other to serve the python/flask application. 


###About Project:
1. This project aims to deploy the flask application built as part of the third project of FSND(https://github.com/jayarajsajjanar/catalog) on the Amazon EC2 linux instance provided by Udacity. 
2. The below configurations steps specified by Udacity as per (https://docs.google.com/document/d/1J0gpbuSlcFa2IQScrTIqI6o3dice-9T7v8EDNjJDfUI/pub?embedded=true) were carried out to complete the project. 

	    a)Launch your Virtual Machine with your Udacity account and log in. You can manage your virtual server at: https://www.udacity.com/account#!/development_environment
	    b)Create a new user named grader and grant this user sudo permissions.
	    c)Update all currently installed packages.
	    d)Configure the local timezone to UTC.
	    e)Change the SSH port from 22 to 2200
    	f)Configure the Uncomplicated Firewall (UFW) to only allow incoming connections for SSH (port 2200), HTTP (port 80), and NTP (port 123)
    	g)Install and configure Apache to serve a Python mod_wsgi application
    	h)Install and configure PostgreSQL:
    		1. Do not allow remote connections
    		2. Create a new user named catalog that has limited permissions to your catalog application database
    	i)Install git, clone and set up your Catalog App project (from your GitHub repository from earlier in the Nanodegree program) so that it functions correctly when visiting your server’s IP address in a browser. Remember to set this up appropriately so that your .git directory is not publicly accessible via a browser!
    	j)Your Amazon EC2 Instance's public URL will look something like this: http://ec2-XX-XX-XXX-XXX.us-west-2.compute.amazonaws.com/ where the X's are replaced with your instance's IP address. You can use this url when configuring third party authentication. Please note the the IP address part of the AWS URL uses dashes, not dots.

## Project Access
#### IP address

**`52.40.16.108`**

#### SSH Port

**`2200`**

#### Web Application URL

`http://52.40.16.108/` -- The application is accessible here. OAuth is configured for this.  

## Softwares Installed

ntp apache2 python-setuptools libapache2-mod-wsgi git pip Flask postgresql postgresql-contrib build-dep python-psycopg2 

## Configuration steps followed:

1. To access the Amazon EC2 instance, Private key was downloaded from 'https://www.udacity.com/account#!/development_environment'. And the steps provided there were followed. 
	1. `mv ~/Downloads/udacity_key.rsa ~/.ssh/`
	2. `chmod 600 ~/.ssh/udacity_key.rsa`
	3. `ssh -i ~/.ssh/udacity_key.rsa root@52.40.16.108`
2. New user was added and sudo permission was given. 
	1. `adduser grader`
	2. `visudo` 
		1. `root ALL=(ALL:ALL) ALL`
    	2. `grader ALL=(ALL:ALL) ALL`
3. To update all currently installed packages.
	1. `sudo apt-get update`
	2. `sudo apt-get upgrade`
4. To configure local time zone to UTC.
	1. `sudo dpkg-reconfigure tzdata` select UTC.
5. To change the SSH port from 22 to 2200.
	1. On the server `sudo nano /etc/ssh/sshd_config` 
		1. Change Port 22 to Port 2200.`
		2. Change `PermitRootLogin without-password` to `PermitRootLogin no`
		3. Change `PasswordAuthentication no` to `PasswordAuthentication yes`. This allows access to the server via password.
		4. Add `UseDNS no` and `AllowUsers grader`. 
		5. `ctrl+x, y and enter` to save the changes in nano editor.
		6. `sudo service ssh restart`
		7. server can be connected to by `ssh grader@52.40.16.108 -p 2200`. Exiting to local can be done by `exit`.
	2. On the local machine, generate public/private key pair using `ssh-keygen`. The private key provided by Udacity was for  the user `root`. The key pair generated now is used to connect to the server as the user `grader`. For additional security password is asked for the private key. (password for securing the key, not for accessing the server)

	   The key pair is placed in the same location as the project(or any other place).  

	    Out of the 2 keys, one is private and the other is public. The contents of the generated public key(the one with .pub) is placed on the server and the private key is used to login from the local. The private key is shared with Udacity evaluators while submitting the project. 

	3. Log into the server as grader and 
		1. `mkdir .ssh`
		2. `touch .ssh/authorized_keys`
		3. copy and paste the contents of the public key from local to `.ssh/authorized_keys`
		4. `chmod 700 .ssh` `chmod 644 .ssh/authorized_keys`
		5. `sudo nano /etc/ssh/sshd_config` and change `PasswordAuthentication yes` to `PasswordAuthentication no`
		6. To resolve the `sudo: unable to resolve host...` warning, 
			1. `sudo nano /etc/hosts` and modify it to 
				```
				52.40.16.108 @ip-10-20-24-202
				127.0.0.1 localhost
				```
		7. Server can be logged into as `grader` using `ssh -i /path to/id_rsa grader@52.40.16.108 -p 2200` without the need for password.
6. To configure the firewall,
	1. Ensure the firewall is inactive using `sudo ufw status`
	2. Everything is set to `deny` using `sudo ufw default deny incoming`. Only the ones that are needed are allowed. This provides additional security to the server.
	3. Allowing only the ones specified by Udacity 

		1. `sudo ufw allow 2200/tcp`
		2. `sudo ufw allow 80/tcp`
		3. `sudo ufw allow 123/udp`
	
	4. Enable the firewall `sudo ufw enable`
7. Installing apache and mod_wsgi:
	1. `sudo apt-get install apache2` visit `http://52.40.16.108` to confirm that it worked.
	2. `sudo apt-get install python-setuptools libapache2-mod-wsgi` 
	3. `.conf` file points to `.wsgi` which in turn points to the file that has `app = Flask(__name__)`.
		a. `sudo nano /etc/apache2/sites-available/000-default.conf` and modify it to include `WSGIScriptAlias / /var/www/linuxconfig/trial.wsgi` 

		`sudo a2ensite 000-default.conf` to enable the site. 

		b. `sudo nano /path/to/trial.wsgi` and modify it to include 


		```python
		#!/user/bin/python
		import sys
		import logging
		logging.basicConfig(stream=sys.stderr)
		sys.path.insert(0,"/var/www/linuxconfig/app/")

		from views import app as application
			application.secret_key = 'Add your secret key'
		```
		c. A dummy file can be returned to check if everything is working fine(before deploying the actual flask app) by pointing the `wsgi` file to it. The dummy file can contain the below content: 

		```python
		from flask import Flask
		app = Flask(__name__)
		@app.route("/")
		def hello():
		    return "Hello world!"
		if __name__ == "__main__":
		  app.run()
		```
	4. Various softwares needed by the flask application was installed :

		```
		sudo apt-get install python-pip
		The softwares in requirements.txt (like SQLAlchemy, Flask-Mail etc) were installed using pip. `virtualenv` can be used for it. 
		```
	5. After every change, apache can be restarted using 

		```
		sudo service apache2 restart
		sudo apache2ctl restart
		```
8. To install and configure PostgreSQL:
	1. `sudo apt-get install postgresql postgresql-contrib`	  `sudo apt-get build-dep python-psycopg2`
	2. To not allow remote connections: `sudo nano /etc/postgresql/9.1/main/pg_hba.conf`. By default, the configuration is set to not allow remote connections. 
	2. (step 9 of the main listing i.e. configuring git and cloning was done before this)New user for postgres - `catalog`. New database - `catalog`.
		1. The original flask app used `sqllite`. The `models` file contained : 

			```python 
			app.config['SQLALCHEMY_DATABASE_URI'] = 'sqllite:///temp/test.db'
			```
			The above line was modified to :

			```python 
			app.config['SQLALCHEMY_DATABASE_URI'] = 'postgresql://catalog:linuxconfig@localhost/catalog2'
			``` 
			In the above line `catalog` is the user in  the database server. `linuxconfig` is the password for the user. 
			`catalog2` is the database. 
		2. To correspond the above changes:
	   		1. `sudo adduser catalog` 
	   		2. `sudo su - postgres` postgres has admin privileges, hence it is made use of to configure `catalog` user.
	   		3. `psql` to connect to the database server. 
	   		4. In the server 
	   			1. `CREATE USER catalog WITH PASSWORD 'linuxconfig';`
	   			2. `ALTER USER catalog CREATEDB;` - `catalog` is given permissions to create databases.
	   			3. `\du` is used to know the roles of users.
	   			4. `CREATE DATABASE catalog WITH OWNER catalog;`
	   			5. `\c catalog` - connect to the user.
	   			6. `REVOKE ALL ON SCHEMA public FROM public;`
	   			7. `GRANT ALL ON SCHEMA public TO catalog;`
	   			8. `\q` and `exit` to return to user `grader`.
	   	3. `python db_create.py` to instantiate the bare minimum. And restart apache. Visit `http://52.40.16.108`. 
	   	4. Connect to postgres and to the `catalog` database and hit `\dt` to ensure that the relations were created
	   	    and instantiated.
9. to configure git and clone the repo of project 3 of FSND:
	1. `sudo apt-get install git`
	2. configure 
		```
		git config --global user.name "YOUR NAME"

		git config --global user.email "YOUR EAMIL"
		```
	3. `git clone https://github.com/jayarajsajjanar/path/to the/repo`
	4.  To ensure .git directory is not publicly accessible via a browser:
		1. `sudo nano .htaccess` and add `RedirectMatch 404 /\.git` to the file.
10. The project is accessible at `http://52.40.16.108`. The OAuth must be configured to serve at the corresponding url. 
	i.e. earlier(original project 3) facebook authentication was available at `localhost:5000`. This needs to be changed to 
	`http://52.40.16.108`




      

