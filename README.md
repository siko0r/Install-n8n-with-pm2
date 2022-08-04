Set up n8n via PM2

### OS Updates
Before doing anything else, update your operating system by running these two commands:
```
sudo apt update && sudo apt upgrade -y
```
#
## Prerequisites
To run n8n via PM2, you need to have the following software installed:
#
### Install Node.js
Add the NodeSource APT repository for Node 16
```
curl -sL https://deb.nodesource.com/setup_16.x | sudo -E bash &&
sudo apt-get install nodejs -y
```
#
### Install NGINX
NGINX server and the SSL configuration requires
```
sudo apt install nginx -y
```
#
### Check Status of NGINX
```
sudo systemctl status nginx
```
#
### Configure NGINX
```
cd /etc/nginx/sites-available/
sudo nano n8n.conf
```
Now insert a copy of the below example configuration and replace
```
server {
    server_name <domain name>;
    listen 80;
    location / {
        proxy_pass http://localhost:5678;
        proxy_set_header Connection '';
        proxy_http_version 1.1;
        chunked_transfer_encoding off;
        proxy_buffering off;
        proxy_cache off;
    }
}
```
#
Now linking the file we have just created
```
sudo ln -s /etc/nginx/sites-available/n8n.conf /etc/nginx/sites-enabled/ &&
sudo nginx -t &&
sudo systemctl reload nginx
```

### Install PM2
```
sudo npm install pm2 -g
```
### Install n8n
```
sudo npm install n8n -g
or update
npm update -g  n8n
```
#
### Start n8n via PM2
```
pm2 start n8n
```
#
### Setup auto-start n8n on machine restart
```
pm2 startup
```
#
#### Restart the n8n service
```
pm2 restart n8n
```
#
### pm2 config
create pm2 config with `pm2 init simple` replce with below code
```
module.exports = {
    apps : [{
        name   : "n8n",
        env: {
            N8N_EMAIL_MODE:"smtp",
            N8N_SMTP_HOST:"smtp.gmail.com",
            N8N_SMTP_PORT:"465",
            N8N_SMTP_USER:"name@mail.com",
            N8N_SMTP_PASS:"app_password",
            N8N_SMTP_SSL:"true",
            GENERIC_TIMEZONE:"Asia/Kolkata",
            TZ:"Asia/Kolkata",
            N8N_BASIC_AUTH_ACTIVE:false,
            WEBHOOK_URL:"https://n8n.domain.in/",
            N8N_HOST:"https://n8n.domain.in/",
            N8N_PROTOCOL:"https",
            N8N_EDITOR_BASE_URL:"https://n8n.domain.in/",
            NODE_FUNCTION_ALLOW_EXTERNAL:"moment,lodash"
        }
    }]
}
```
#### Start with pm2 config
```pm2 start ecosystem.config.js```

#### Start with pm2 with update config
```--pm2 restart ecosystem.config.js --update-env```
