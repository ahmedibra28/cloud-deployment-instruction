# How to deploy complete NodeJS Web Application to Cloud. e.g. DigitalOcean

### Update the server

---

- `sudo apt update`
- `sudo apt upgrade`
- `cd /home`

### Clone Project From Github

---

`git clone https://github.com/ahmaat19/<repository_name>.git `

> If it necessary to switch branches do the following:

- `cd /<repository_name>`
- `git switch <branch_name>`

### Install Nodejs through NVM

---

- `cd /`

```
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v<latest_version>/install.sh | bash
```

- `sudo reboot`
- ` nvm install <node_version>`

### Install NGINX

---

- `sudo apt update`
- `sudo apt install nginx`
- `sudo ufw app list`
- `sudo ufw allow 'Nginx HTTP'`
- `systemctl status nginx`
- `sudo systemctl restart nginx`

#### Config The nginx.conf

---

- `sudo nano /etc/nginx/sites-available/<your_domain>`

> copy and paste the following:

```shell
server {
    #listen       80;
    server_name  mamosbusiness.com www.mamosbusiness.com;

    access_log /root/server_logs/host.access.log;


    location / {
        proxy_set_header   X-Forwarded-For $remote_addr;
        proxy_set_header   Host $http_host;
        proxy_pass         http://127.0.0.1:3000;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;

    }
    error_page   500 502 503 504  /50x.html;
    location = /50x.html {
        root   /usr/share/nginx/html;
    }

    server_tokens off;

    location ~ /\.ht {
        deny  all;
    }

}
```

`sudo ln -s /etc/nginx/sites-available/<your_domain> /etc/nginx/sites-enabled/`

### Install MONGO Database

---

- `sudo apt-get install gnupg`

```
curl -fsSL https://pgp.mongodb.com/server-6.0.asc | \
   sudo gpg -o /usr/share/keyrings/mongodb-server-6.0.gpg \
   --dearmor
```

- `echo "deb [ arch=amd64,arm64 signed-by=/usr/share/keyrings/mongodb-server-6.0.gpg ] https://repo.mongodb.org/apt/ubuntu jammy/mongodb-org/6.0 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-6.0.list`
- `sudo apt-get update`
- `sudo apt-get install -y mongodb-org`

```
echo "mongodb-org hold" | sudo dpkg --set-selections
echo "mongodb-org-database hold" | sudo dpkg --set-selections
echo "mongodb-org-server hold" | sudo dpkg --set-selections
echo "mongodb-mongosh hold" | sudo dpkg --set-selections
echo "mongodb-org-mongos hold" | sudo dpkg --set-selections
echo "mongodb-org-tools hold" | sudo dpkg --set-selections
```

- `sudo systemctl start mongod.service`
- `sudo systemctl status mongod`
- `sudo systemctl enable mongod`



### Install PM2 to run nodejs in the background

---

`npm install -g pm2`

> run server.js

`pm2 start server.js`

> run if it's reactjs

`pm2 start --name <app_name> npm -- start`

`pm2 unstartup`

`pm2 save`

### Installing Certbot /SSL

---

`sudo apt install certbot python3-certbot-nginx`

> add this line

`sudo nano /etc/nginx/sites-available/<domain_name>`

`server_name <example.com> <www.example.com>;`

> confirm

`sudo nginx -t`
`sudo systemctl reload nginx`

> Certbot can now find the correct server block and update it automatically.

### Allowing HTTPS Through the Firewall

```
sudo ufw status
sudo ufw allow 'Nginx Full'
sudo ufw delete allow 'Nginx HTTP'
```

### Obtaining an SSL Certificate

`sudo certbot --nginx -d <example.com> -d <www.example.com>`

### Verifying Certbot Auto-Renewal

`sudo systemctl status certbot.timer`

> To test the renewal process, you can do a dry run with certbot:

`sudo certbot renew --dry-run`
