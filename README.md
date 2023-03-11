# Node.js Deployment Guide

# Note: it's recommended to not use the root user for the following steps. Instead, create a non-root user with sudo privileges and use that user.

> To create a non-root user:
```
adduser <username>
```
> By adding your new user to the sudo group with the following command, you will grant them sudo privileges and the ability to perform actions with elevated privileges:
```
usermod -aG sudo <username>
```
> Log out of the root user account with the following command:
```
exit
```
> Log in to your server with your new user account using SSH:
```
ssh <username>@your_server_ip_address
```
> You can now use your new user account to perform actions with elevated privileges by prepending the sudo command to your commands.

# This guide will walk you through the steps to deploy a Node.js app on DigitalOcean using PM2, NGINX as a reverse proxy, and an SSL from Let's Encrypt.

## 1. Create a Free DigitalOcean Account and Get $200 Credit

> To get started, create a free DigitalOcean account using the link below and get a free $200 credit:
       https://m.do.co/c/6146a51374b2

## 2. Create and Launch a Droplet and SSH into the Machine

> Next, create a droplet on DigitalOcean and SSH into the machine.

## 3. Install Node.js and NPM

> Install Node.js and NPM by running the following commands:

```
curl -sL https://deb.nodesource.com/setup_18.x | sudo -E bash -
sudo apt install nodejs

node --version
```

## 4. Clone Your Project from GitHub

> Clone your project from GitHub by running the following command:

```
git clone "your project link"
```

## 5. Install Dependencies and Test the App

> Install the necessary dependencies and test the app by running the following commands:

```
sudo npm i pm2 -g
pm2 start index

# Other pm2 commands
pm2 show app
pm2 status
pm2 restart app
pm2 stop app
pm2 logs (Show log stream)
pm2 flush (Clear logs)

# To make sure app starts when reboot
pm2 startup ubuntu
```

## 6. Set Up the Firewall

> Set up the firewall by running the following commands:

```
sudo ufw enable
sudo ufw status
sudo ufw allow ssh (Port 22)
sudo ufw allow http (Port 80)
sudo ufw allow https (Port 443)
```

## 7. Install NGINX and Configure

> Install NGINX and configure it by running the following commands:

```
sudo apt install nginx

sudo nano /etc/nginx/sites-available/default
```
> Add the following code to the location part of the server block:
```
    server_name yourdomain.com www.yourdomain.com;

    location / {
        proxy_pass http://localhost:8001; #whatever port your app runs on
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
    }
```
>  Check the NGINX config and restart NGINX by running the following commands:
```
# Check NGINX config
sudo nginx -t

# Restart NGINX
sudo nginx -s reload
```

## 8. Add SSL with Let's Encrypt

> Finally, add an SSL certificate from Let's Encrypt by running the following commands:

```
sudo add-apt-repository ppa:certbot/certbot
sudo apt-get update
sudo apt-get install python3-certbot-nginx
sudo certbot --nginx -d yourdomain.com -d www.yourdomain.com

# Only valid for 90 days, test the renewal process with
certbot renew --dry-run
```
> That's it! Your Node.js app should now be up and running on DigitalOcean with PM2, NGINX, and SSL from Let's Encrypt.
