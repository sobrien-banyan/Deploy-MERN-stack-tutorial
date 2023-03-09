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
https://github.com/sobrien-banyan/snake-game

https://github.com/sobrien-banyan/snake-game-backend

 

## Step to create mongo data base on MongoDB Atlas

1. Sign up or login to [MongoDB Atlas](https://www.mongodb.com/cloud/atlas/register). You can follow the tutorial for creating a mongo database or follow the steps below.
2. On the home page select `New Project`
3. Select `Add Current IP Address`. Network Access will need to be updated to accept all IP address.
4. Select `Build a Database`.
5. Select `Free tier`. AWS for the provider and then select `Create`. 
6. On the left handside nav bar select `Network Access`.
7. Select `+ ADD IP ADDRESS`. Enter `0.0.0.0/0` for `Access List Entry:` and click `Confirm`.
8. Naviage to the project page titled `Data Deployments`. You should be on the Database Deployments page. Create an cluster by selecing `Create`.
9. Select `Shared`, `AWS` for the Cloud Provider, Region `N. Virginia`. Then Select `Create Cluster`.
10. Select the cluster, then Select `Collections`.
11. Select `+ Create Database`. For Database name enter `snake_game`. For Collection name enter `name_score` and press `Create `
12. Insert some data so we can test it later. Select `INSERT DOCUMENT`. Place the follow in the input and then press `Insert`:
```
{
  "name": "Shaun",
  "score": "10"
}
```
13. On the left handside nav bar select `Database Access` and then select `+ ADD NEW DATABASE USER`
14. Enter a name and password. Make sure the password has no special character in it. Save the name and password in a document, you'll need to later to construct the connection string. Select `Specific Privilege`, select `Add Specific Privilege`. The first dropdown select `readWrite`. Second input enter `snake_game`. Third input enter `name_score`. Toggle `Restrict Access to Specific Clusters/Federated Database Instances` and select the cluster the snake_game is in. Last click `Add User`.
15. On the left handside nav bar select `Database`. Select `Connect`. Select `Connect your application`. Copy the connection string, it will look like this `mongodb+srv://<username>:<password>@cluster0.v6q3wky.mongodb.net/?retryWrites=true&w=majority`. `<username>` and `<password>` are placeholders. Replace them with the name and password of user creacted in step the above step. This connection string will be added to the server config.env file in the EC2 instance using SSH.



## Steps taken to create server on an aws EC2 instance:

1. Create an EC2 instance on [aws](https://aws.amazon.com/) and host the express server
2. Navigte to the EC2 dashboard and select Launch Instance
3. Enter name
4. Under Application and OS Images (Amazon Machine Image) select `Ubuntu`
5. Under Key pair (login), select `create new key pair`. This will be used later to connect to the server through ssh terminal. A .pem file will be downloaded. Place it in a folder.
6. Under Network settings select `Allow HTTPS traffic from the Internet` and `Allow HTTP traffic from the internet`
7. Select `Launch instance`
8. Select `Connect to instance`
9. Select `SSH client`. There are two commands in the `SSH client` panel that need to be ran in a terminal. Examples: `chmod 400 <name-of-key>.pem` and `ssh -i "<name-of-key>.pem" ubuntu@ec2-3-83-25-6.compute-1.amazonaws.com`
10. Next we need to update the inbound rules on the EC2 instances. Select the EC2 instance... `EC3 > Instance > i-xxxxxxx`. Select the `Security` tab. Above Inbound rules select the link under Security groups e.g. `sg-01xxxxxxx(launch-wizard-7)`. Under Inbound rules select `Edit inbound rules`. Select `Add rule`. For Type dropdown select `HTTP` and Source dropdown select `Anywhere-IPv4` and click `Save rules`.  
11.  Next we need to login to the Ubuntu server hosted an aws through a terminal using the encoded .pem file. On a mac desktop open a terminal window and cd into the folder that contains the .pem file.
12. Run `ls` command. You should see the .pem file. 
13. Run the command `chmod 400 <name-of-key>.pem`
14. Run the command `ssh -i "<name-of-key>.pem" ubuntu@ec2-3-83-25-6.compute-1.amazonaws.com` enter `yes` and press return. You should see something like this in the terminal `ubuntu@ip-172-31-92-8:~/`
15. Next clone the repo `git clone https://github.com/sobrien-banyan/portfolio-server.git`
16. Cd into the repo
17. Run the following command to install node `curl -sL https://deb.nodesource.com/setup_16.x | sudo -E bash -`. In this repo version 16.x is used.
18. Run the following command to finish intalling node.js and npm`sudo apt-get install -y nodejs`.
19. you might need to update npm by running this `sudo npm install -g npm@9.5.0`
20. Install the packages by running `npm install`
21. To keep the server running we need to install PM2 in the root directory. Enter the following command to get in the root directory `cd /` then run the follow command to install pm2 `sudo npm install pm2 -g`.
22. Install NGINX by running the following command in the root directory `sudo apt install nginx-core`.
23. Delete the default NGINX site config file with the command `sudo rm /etc/nginx/sites-available/default`.
24. Launch the nano text editor to create an new default site config file with `sudo nano /etc/nginx/sites-available/default`. A nano edit window will pop up. Paste the following: 
    ```
    server {
        listen 80;
        listen [::]:80;
        server_name _;

        # node api reverse proxy
        location / {
        proxy_pass http://localhost:4000/;
        }
    }
    ```
25. Press `y` and then enter.
26. Restart NGINX by running the following command `sudo systemctl reload nginx`
27. Navigate to the repo by entering `cd ~` then `ls` then cd into the repo.
28. Enter the ATLAS_URI in the config.env by enter the following command `nano config.env` grab the atlas uri from atlas mongodb and press `control x` and enter `y` then enter. Example of atlas uri: `ATLAS_URI=mongodb+srv://snake_game_user_access:v6flR0FC2ijcEOdR@cluster0.v6q3wky.mongodb.net/?retryWrites=true&w=majority`.
29. Start the server by running `pm2 start server.js`
30. Ping the server by grabbing the public IPv4 address from AWS. Put it in the browser address bar `http://18.210.17.248/record`. You should get back the record from atlas mongodb


## Steps for creating an SSL certificate and using it to connect to the server hosted on an EC2 instance.

### overview: Registering a domain name and using certbot to create an SSL certificate in SSH terminal.

- resource: [certbot](https://certbot.eff.org/instructions?ws=nginx&os=ubuntufocal)

1. Register a domain name. I used [namecheap](https://www.namecheap.com/). Cost is a few dollor. Copy and save the domain name.
- Login to aws, navigate to the EC2 instance and grab the `Public IPv4 address`. It should look like `3.83.26.5`.
- login in to namecheap. On the dashboard find the domain name, on the left select `MANAGE`.
- Select `Advance DNS` and then select `+ ADD NEW RECORD`
- First dropdown (Type) select `A Record`
- Second input (Host) enter `@`
- Third input (Value) enter the `Public IPv4 address` from the aws EC2 instance.
- Fourth dropdown (TTL) select `Automatic`
- Select the green check mark to save the new record. 
2. Next we need to login to the Ubuntu server hosted an aws through a terminal using the encoded .pem file. On a mac desktop open a terminal window and cd into the folder that contains the .pem file.
3. Run `ls` command. You should see the .pem file. 
4. Run the command `chmod 400 <name-of-key>.pem`. `<name-of-key>` is a place holder. This the following command can be found on aws EC2 instance under `Connect to Instance` and  `SSH Client`.
5. Run the command `ssh -i "<name-of-key>.pem" ubuntu@ec2-3-83-25-6.compute-1.amazonaws.com` enter `yes` and press return. You should see something like this in the terminal `ubuntu@ip-172-31-92-8:~/`
6. Launch the nano text editor to create an new default site config file with `sudo nano /etc/nginx/sites-available/default`. A nano edit window will pop up. `<domain name>` is a placeholder in the code below and will need to be replace with the domain name from step one. Paste the following: 
    ```
    server {
        listen 80;
        listen [::]:80;
        server_name <domain name>;

        # node api reverse proxy
        location / {
        proxy_pass http://localhost:4000/;
        }
    }
    ```
7. Press `y` and then enter.
8. Restart NGINX by running the following command `sudo systemctl reload nginx`
9. We need to remove and reinstll certbot. Run the following commands:
- `cd /`
- `lsb_release -a`
- `sudo snap install core; sudo snap refresh core`
- `sudo apt-get remove certbot`
- `sudo snap install --classic certbot`
- `sudo certbot --nginx`
- Enter your email address and press return.
- Read the terms and enter `y`
- Enter `y` again
- Enter the domain name from step one. Press return
- If the terminal returns `Successfully deployed` you're good to go. If not run the following command, replacing `<domain name>` with domain from step one: `sudo certbot install --cert-name <domain name>`
- You are now ready to test. Place the following command in a browser address bar. `https://<domain name>/record`. You should get back the record from the mongodb. Use the same URL in your front-end code when making an api call. E.g. `await axios.get('https://<domain name>/record');`
 

## Steps to deploy front end repo to [Netlify.com](https://www.netlify.com/)

1. Log into or create a netlify account
2. From the dashboard select `Add new site` and select from the dropdown `Import an existing project`
3. Select `GitHub` and grant netlify permission to access your GitHub repo. Once permission is granted you should be on step two 'Pick a repository'. Pick the forked repo 'snake-game' from your github account.
4. Select `Deploy site`. It will take time to deploy the site.
5. Next we add the email address take will receive the form submition.
- From the dashboard select the site you just deployed.
- Select `Site settings`
- From the left side nav section select `Forms` that will open more option below it and select `Form notifications`
- Select `Add notification` and then select `Email notification`
- Under `Email to notify` enter your email address
- Under `Form` select from the dropdown `contact` and then select `Save`
- Test it by going the site and submit the contact form. You should receive an email with the input form data.
- F.Y.I. there is a hidden form in index.html thats connect the the Contact.jsx form that makes it work.

## To go forward you will need to deploy the back end repo: https://github.com/sobrien-banyan/snake-game-backend. Once you are done with deploying the back end, you will need to insert the server domain name into the .env file using netlify's dashboard.

6. From the dashboard select the website.
7. Select `Site settings`
8. Select `Environment variables`
9. Select `Add a variable`
10. For the `Key` input enter `REACT_APP_SERVER_DOMAIN`
11. For the `Values` input enter the domain for your backend server.
12. Select `Create variable`
13. The site needs to be redeplyed. Select the website from the dashboard. Select `Deploys`
14. Select `Trigger deploy` then select `Deploy site`
15. All done!


## The following steps are for using a custom domain name from namecheap for the application

16. Register a domain name at [namecheap](https://www.namecheap.com/). Cost is a few dollor.
17. Go to netlify, select the site and then select `Domains`
18. Select `Add or register domain`
19. For input `Domain of subdomain` enter the domain name from netlify and click `Verify`. You may be asked to Activate Netlify DNS.
20. From `Domains` select the domain and it will take you to `DNS settings`. Find section `Name servers`. Copy the four name servers. They will look something like `dns.p01.nsone.net`. The name will need to by copied your namecheap account.
21. Go to the namecheap dashboard. Select `Domain List`
22. Find the domain name and select `Manage`
23. Scroll down and find the `NAMESERVERS` section. Select `+ ADD NAMESERVER` and copy the 'Name servers' from Netlify one-by-one. e.g. `dns.p01.nsone.net` 
24. All done!
