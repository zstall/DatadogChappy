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


