# DatadogChappy
A simple flask app to deploy and run APM and Traces from

To get the flask app working, I followed this article. I'm going to provide a seperate outline as to get Chappy working there are a few extra steps.
- Here is the article for reference: https://medium.com/techfront/step-by-step-visual-guide-on-deploying-a-flask-application-on-aws-ec2-8e3e8b82c4f7 

Step 1: Create an Ubuntu EC2 on AWS

- Log in to AWS Console.
- Go to EC2 Section and select Ubuntu 18.4 AMI
- Select t2.micro if you want to stay in free tier or any other instance type you want.
- Press Next until Security Groups.
- Allow HTTP (Port 80), SSH (Port 22), HTTPS (Port 443) inbound traffic and press next.
- Create/Reuse Key-pair for connecting with your instance.


Step 2: SSh into Ubuntu EC2
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
    $ python3 -m venv venv
    
    // Activate the virtual environment
    $ source venv/bin/activate
    
    // Install Flask
    $ pip install Flask
    
    // Other important packages for Chappy:
    $ sudo apt-get install postgresql
    $ sudo apt-get install postgresql-contrib
    $ python3 -m pip install psycopg2-binary
    $ python3 -m pip install flask-restful
    $ python3 -m pip install json_log_formatter
    
    ```
    
    
    
    
    
