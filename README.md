###*******************linuxconfig******************

###             Fifth project as part of the Udacity/Google - Full Stack Web Developer - Nanodegree
 
###Key things learnt:
1. Configuring ssh access(sshd_config file) to a Amazon EC2 server via password as well as via public/private key.
2. Creating user and configuring the access permissions of the user.
3. Configuring ports and firewall.
4. Installing and using apache, python mod_wsgi, postgres and git. Using virtualenv. 
5. Learnt how various modules like apache, mod_wsgi, interacted with each other to serve the python/flask application. 


###About Project:
1. This project aims to deploy the flask application built as part of the third project of FSND(https://github.com/jayarajsajjanar/catalog) on the Amazon EC2 linux instance provided by Udacity. 
2. The below configurations steps specified by Udacity as per (https://docs.google.com/document/d/1J0gpbuSlcFa2IQScrTIqI6o3dice-9T7v8EDNjJDfUI/pub?embedded=true) were carried out. 

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
    	
###Development Environment :
1. The virtual machine used/provided is Ubuntu 14.04, 32 bits.
2. Database engine - SQLlite.
3. Python 2.7.3
4. Flask framework, ORM -> Flask_SQLAlchemy, Template Engine - Jinja2, Forms - WTF

###Steps for running the project:
1. **Clone** the repo of the current project using - `git clone https://github.com/jayarajsajjanar/catalog.git` 
2.  Use virtualenv and the requirements.txt provided to install necessary libraries and softwares.
3.  Need to have facebook login details for editing/deleting. (Oauth is provided for Facebook only)
2. `cd app` and then  `python db_create.py` to create the database and to instantiate minimally required objects.
3. `cd .` to return to parent directory. Hit `python run.py` to run the project. Visit `localhost:5000` on your browser.
4.  Have fun adding/deleting/editing categories and items.
5.  API endpoint is provided at `\all_items.json`

      

