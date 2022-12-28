# DatadogChappy
A simple flask app to deploy and run APM and Traces from

To get the flask app working, I followed this article. I'm going to provide a seperate outline as to get Chappy working there are a few extra steps.
- Here is the article for reference: https://medium.com/techfront/step-by-step-visual-guide-on-deploying-a-flask-application-on-aws-ec2-8e3e8b82c4f7 

##Step 1: Create an Ubuntu EC2 on AWS

- Log in to AWS Console.
- Go to EC2 Section and select Ubuntu 18.4 AMI
- Select t2.micro if you want to stay in free tier or any other instance type you want.
- Press Next until Security Groups.
- Allow HTTP (Port 80), SSH (Port 22), HTTPS (Port 443) inbound traffic and press next.
- Create/Reuse Key-pair for connecting with your instance.


##Step 2: SSh into Ubuntu EC2
- Open a terminal
- Type `$ ssh -i <your key name>.pem ubuntu@<Public DNS of your EC2>`.
- If you encounter an error message, run $ chmod 400 <your key name>.pem then try Step 2 again.


Step 3: Create/Clone Flask application inside EC2
(NOTE: I performed all of the following as the root user)

- Install Python Virtualeng
    ```
    $ sudo apt-get update
    $ sudo apt-get install python3-venv
    ```
    
- Activate the new virtual environment in a new directory
    ```
    // Create directory
    $ mkdir chappy
    $ cd chappy
    
    // Create the virtual environment
    $ python3 -m venv chp
    
    // Activate the virtual environment
    $ source chp/bin/activate
    
    // Install Flask
    $ pip install Flask
    
    // Other important packages for Chappy:
    $ sudo apt-get install postgresql
    $ sudo apt-get install postgresql-contrib
    $ python3 -m pip install psycopg2-binary
    $ python3 -m pip install flask-restful
    $ python3 -m pip install json_log_formatter
    
    ```
    
- Setup Chappy DB

    Create a user mapping for the ubuntu user to postgres:
    ```
    $  sudo vi /etc/postgresql/14/main/pg_ident.conf
    ```
    
    Add an entry mapping ubuntu to postgres:
    ```
    # MAPNAME       SYSTEM-USERNAME         PG-USERNAME
    user1           ubuntu                  postgres
    ```
    
    Add an entry for postgres into the pg_hba.conf
    ```
    $  sudo vi /etc/postgresql/14/main/pg_hba.conf
    ```
    add:
    ```
    # Database administrative login by Unix domain socket
    local   all             postgres                                md5
    local   all             admin                                   md5
    ```
    
    Now we are ready to connect to postgres and set up the db. Login to postgres with the following command:
    ```
    psql -U postgres
    ```
    
    Here we are going to setup Postgres, Admin, and pgcrypto:
    ```
    $ ALTER USER postgres PASSWORD '<YOUR NEW PASSWORD>';
    $ CREATE DATABASE yourdbname;
    $ CREATE USER youruser WITH ENCRYPTED PASSWORD 'yourpass';
    $ GRANT ALL PRIVILEGES ON DATABASE yourdbname TO youruser;
    $ \q
    ```
    Sign in as admin:
    ```
    $ pswl -U admin -d chappy
    $ CREATE EXTENSION IF NOT EXISTS pgcrypto;
    $ \q
    ```
    
    Now let's add some tables and data to our database, in the chappy directory run the create_chappy_db.py script:
    ```
    $ python3 create_chappy_db.py
    ```
    
    You can now login to the chappy database and make sure  the data is there and ready to use! Chappy is now 90% setup, and we'll just need to configure the application to be accessible on the internet!
    
    
##Step 3: Run Gunicorn WSGI server to serve the Flask Application
    
    Install Gunicorn using
    ```
    $ pip install gunicorn

    ```
    
    You may need to install gunicorn for Ubuntu if you are getting a command not recognized error:
    ```
    $ sudo apt install gunicorn
    ```
    
    Test and Run Gunicorn — '$ gunicorn -b 0.0.0.0:8000 fchap:app' . Further modifications are possible, check out the gunicorn docs!
    Gunicorn is running (Ctrl + C to exit gunicorn)!
    
    
##Step 4: Use systemd to manage Gunicorn
    Systemd is a boot manager for Linux. We are using it to restart gunicorn if the EC2 restarts or reboots for some reason.

    We create a <projectname>.service file in the /etc/systemd/system folder, and specify what would happen to gunicorn when the system reboots.

    We will be adding 3 parts to systemd Unit file — Unit, Service, Install

    Unit — This section is for description about the project and some dependencies
    Service — To specify user/group we want to run this service after. Also some information about the executables and the commands.
    Install — tells systemd at which moment during boot process this service should start.
    
    With that said, create an unit file in the /etc/systemd/system directory
    
    ```
    $ sudo vim /etc/systemd/system/chappy.service
    ```
    Then add this into the file:
    ```
    [Unit]
    Description=Gunicorn instance for a simple flask app
    After=network.target
    [Service]
    User=ubuntu
    Group=ubuntu
    WorkingDirectory=/home/ubuntu/chappythreezero
    ExecStart=/home/ubuntu/chappythreezero/chp/bin/gunicorn -b localhost:8000 fchap:app
    Restart=always
    [Install]
    WantedBy=multi-user.target
    ```
    
    
