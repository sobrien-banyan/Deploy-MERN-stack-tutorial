# Deploy-MERN-stack-tutorial

## Overview:
This tutorial will take you through the steps to deploy a MERN stack application using a front-end repo and a back-end repo. The app is a simple snake game built with React. Section one lays out the steps to deploy the back end. Section two lays out the steps to deploy the front end.

This can be used as a foundation for a portfolio.

## Tech and resources:
Front-end repo: https://github.com/sobrien-banyan/snake-game

Back-end repo: https://github.com/sobrien-banyan/snake-game-backend

[GitHub](https://github.com/)

[Netlify.com](https://www.netlify.com/)

[namcheap.com](https://www.namecheap.com/)

[MongoDB Atlas](https://www.mongodb.com/cloud/atlas/register)

[AWS](https://aws.amazon.com/)

[cerbot](https://certbot.eff.org/instructions?ws=nginx&os=ubuntufocal)

[nginx](https://www.nginx.com/)

[w3Schools](https://www.w3schools.com/)

 

Fork both the front end and back end repos to your GitHub account.
GitHub - sobrien-banyan/snake-game 

GitHub - sobrien-banyan/snake-game-backend 

 

## Section one:
Step to create mongo data base on MongoDB Atlas
Sign up or login to MongoDB Atlas. You can follow the tutorial for creating a mongo database or follow the steps below.

On the home page select New Project

Select Add Current IP Address. Network Access will need to be updated to accept all IP address.

Select Build a Database.

Select Free tier. AWS for the provider and then select Create.

On the left hand-side nav bar select Network Access.

Select + ADD IP ADDRESS. Enter 0.0.0.0/0 for Access List Entry: and click Confirm.

Navigate to the project page titled Data Deployments. You should be on the Database Deployments page. Create an cluster by selecting Create.

Select Shared, AWS for the Cloud Provider, Region N. Virginia. Then Select Create Cluster.

Select the cluster, then Select Collections.

Select + Create Database. For Database name enter snake_game. For Collection name enter name_score and press Create

Insert some data so we can test it later. Select INSERT DOCUMENT. Place the follow in the input and then press Insert:


{
  "name": "Shaun",
  "score": "10"
}

On the left hand-side nav bar select Database Access and then select + ADD NEW DATABASE USER

Enter a name and password. Make sure the password has no special character in it. Save the name and password in a document, you'll need to later to construct the connection string. Select Specific Privilege, select Add Specific Privilege. The first dropdown select readWrite. Second input enter snake_game. Third input enter name_score. Toggle Restrict Access to Specific Clusters/Federated Database Instances and select the cluster the snake_game is in. Last click Add User.

On the left hand-side nav bar select Database. Select Connect. Select Connect your application. Copy the connection string, it will look like this mongodb+srv://<username>:<password>@cluster0.v6q3wky.mongodb.net/?retryWrites=true&w=majority. <username> and <password> are placeholders. Replace them with the name and password of user created in the above step. This connection string will be added to the server config.env file in the EC2 instance using SSH.

Steps taken to create server on an aws EC2 instance:
Create an EC2 instance on aws and host the express server

Navigte to the EC2 dashboard and select Launch Instance

Enter name

Under Application and OS Images (Amazon Machine Image) select Ubuntu

Under Key pair (login), select create new key pair. This will be used later to connect to the server through ssh terminal. A .pem file will be downloaded. Place it in a folder.

Under Network settings select Allow HTTPS traffic from the Internet and Allow HTTP traffic from the internet

Select Launch instance

Select Connect to instance

Select SSH client. There are two commands in the SSH client panel that need to be ran in a terminal. Examples: chmod 400 <name-of-key>.pem and ssh -i "<name-of-key>.pem" ubuntu@ec2-3-83-25-6.compute-1.amazonaws.com

Next we need to update the inbound rules on the EC2 instances. Select the EC2 instance... EC3 > Instance > i-xxxxxxx. Select the Security tab. Above Inbound rules select the link under Security groups e.g. sg-01xxxxxxx(launch-wizard-7). Under Inbound rules select Edit inbound rules. Select Add rule. For Type dropdown select HTTP and Source dropdown select Anywhere-IPv4 and click Save rules.

Next we need to login to the Ubuntu server hosted an aws through a terminal using the encoded .pem file. On a mac desktop open a terminal window and cd into the folder that contains the .pem file.

Run ls command. You should see the .pem file.

Run the command chmod 400 <name-of-key>.pem

Run the command ssh -i "<name-of-key>.pem" ubuntu@ec2-3-83-25-6.compute-1.amazonaws.com enter yes and press return. You should see something like this in the terminal ubuntu@ip-172-31-92-8:~/

Next clone the repo git clone https://github.com/sobrien-banyan/portfolio-server.git

Cd into the repo

Run the following command to install node curl -sL https://deb.nodesource.com/setup_16.x | sudo -E bash -. In this repo version 16.x is used.

Run the following command to finish installing node.js and npmsudo apt-get install -y nodejs.

you might need to update npm by running this sudo npm install -g npm@9.5.0

Install the packages by running npm install

To keep the server running we need to install PM2 in the root directory. Enter the following command to get in the root directory cd / then run the follow command to install pm2 sudo npm install pm2 -g.

Install NGINX by running the following command in the root directory sudo apt install nginx-core.

Delete the default NGINX site config file with the command sudo rm /etc/nginx/sites-available/default.

Launch the nano text editor to create an new default site config file with sudo nano /etc/nginx/sites-available/default. A nano edit window will pop up. Paste the following:


server {
    listen 80;
    listen [::]:80;
    server_name _;

    # node api reverse proxy
    location / {
    proxy_pass http://localhost:4000/;
    }
}

Press y and then enter.

Restart NGINX by running the following command sudo systemctl reload nginx

Navigate to the repo by entering cd ~ then ls then cd into the repo.

Enter the ATLAS_URI in the config.env by enter the following command nano config.env grab the atlas uri from atlas mongodb and press control x and enter y then enter. Example of atlas uri: ATLAS_URI=mongodb+srv://snake_game_user_access:v6flR0FC2ijcEOdR@cluster0.v6q3wky.mongodb.net/?retryWrites=true&w=majority.

Start the server by running pm2 start server.js

Ping the server by grabbing the public IPv4 address from AWS. Put it in the browser address bar http://18.210.17.248/record. You should get back the record from atlas mongodb

Steps for creating an SSL certificate and using it to connect to the server hosted on an EC2 instance.
overview: Registering a domain name and using certbot to create an SSL certificate in SSH terminal.
resource: certbot

Register a domain name. I used namecheap. Cost is a few dollors. Copy and save the domain name.

Login to aws, navigate to the EC2 instance and grab the Public IPv4 address. It should look like 3.83.26.5.

login in to namecheap. On the dashboard find the domain name, on the left select MANAGE.

Select Advance DNS and then select + ADD NEW RECORD

First dropdown (Type) select A Record

Second input (Host) enter @

Third input (Value) enter the Public IPv4 address from the aws EC2 instance.

Fourth dropdown (TTL) select Automatic

Select the green check mark to save the new record.

Next we need to login to the Ubuntu server hosted an aws through a terminal using the encoded .pem file. On a mac desktop open a terminal window and cd into the folder that contains the .pem file.

Run ls command. You should see the .pem file.

Run the command chmod 400 <name-of-key>.pem. <name-of-key> is a place holder. This the following command can be found on aws EC2 instance under Connect to Instance and SSH Client.

Run the command ssh -i "<name-of-key>.pem" ubuntu@ec2-3-83-25-6.compute-1.amazonaws.com enter yes and press return. You should see something like this in the terminal ubuntu@ip-172-31-92-8:~/

Launch the nano text editor to create an new default site config file with sudo nano /etc/nginx/sites-available/default. A nano edit window will pop up. <domain name> is a placeholder in the code below and will need to be replace with the domain name from step one. Paste the following:


server {
    listen 80;
    listen [::]:80;
    server_name <domain name>;

    # node api reverse proxy
    location / {
    proxy_pass http://localhost:4000/;
    }
}

Press y and then enter.

Restart NGINX by running the following command sudo systemctl reload nginx

We need to remove and reinstall certbot. Run the following commands:

cd /

lsb_release -a

sudo snap install core; sudo snap refresh core

sudo apt-get remove certbot

sudo snap install --classic certbot

sudo certbot --nginx

Enter your email address and press return.

Read the terms and enter y

Enter y again

Enter the domain name from step one. Press return

If the terminal returns Successfully deployed you're good to go. If not run the following command, replacing <domain name> with domain from step one: sudo certbot install --cert-name <domain name>

You are now ready to test. Place the following command in a browser address bar. https://<domain name>/record. You should get back the record from the mongodb. Use the same URL in your front-end code when making an api call. E.g. await axios.get('https://<domain name>/record');

 

## Section two:
Steps to deploy front end repo to Netlify.com
Log into or create a Netlify account

From the dashboard select Add new site and select from the dropdown Import an existing project

Select GitHub and grant Netlify permission to access your GitHub repo. Once permission is granted you should be on step two 'Pick a repository'. Pick the forked repo 'snake-game' from your GitHub account.

Select Deploy site. It will take time to deploy the site.

Next we add the email address take will receive the form submission.

From the dashboard select the site you just deployed.

Select Site settings

From the left side nav section select Forms that will open more option below it and select Form notifications

Select Add notification and then select Email notification

Under Email to notify enter your email address

Under Form select from the dropdown contact and then select Save

Test it by going the site and submit the contact form. You should receive an email with the input form data.

F.Y.I. there is a hidden form in index.html thats connect the the Contact.jsx form that makes it work.

To go forward you will need to deploy the back end repo: GitHub - sobrien-banyan/snake-game-backend . Once you are done with deploying the back end, you will need to insert the server domain name into the .env file using Netlify's dashboard.
From the dashboard select the website.

Select Site settings

Select Environment variables

Select Add a variable

For the Key input enter REACT_APP_SERVER_DOMAIN

For the Values input enter the domain for your backend server.

Select Create variable

The site needs to be redeployed. Select the website from the dashboard. Select Deploys

Select Trigger deploy then select Deploy site

All done!

The following steps are for using a custom domain name from namecheap for the application
Register a domain name at namecheap. Cost is a few dollors.

Go to netlify, select the site and then select Domains

Select Add or register domain

For input Domain of subdomain enter the domain name from netlify and click Verify. You may be asked to Activate Netlify DNS.

From Domains select the domain and it will take you to DNS settings. Find section Name servers. Copy the four name servers. They will look something like dns.p01.nsone.net. The name will need to by copied your namecheap account.

Go to the namecheap dashboard. Select Domain List

Find the domain name and select Manage

Scroll down and find the NAMESERVERS section. Select + ADD NAMESERVER and copy the 'Name servers' from Netlify one-by-one. e.g. dns.p01.nsone.net

All done!
