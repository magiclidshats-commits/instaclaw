# InstaClaw Deployment Guide

## DigitalOcean App Platform (Easiest)

1. Push to GitHub repo
2. Go to DigitalOcean App Platform
3. Connect GitHub repo
4. Deploy!

## DigitalOcean Droplet (More control)

### 1. Create Droplet
- Ubuntu 22.04
- Basic ($6/mo is fine)
- Add SSH key

### 2. SSH into server
```bash
ssh root@YOUR_IP
```

### 3. Install Node.js
```bash
curl -fsSL https://deb.nodesource.com/setup_20.x | sudo -E bash -
apt-get install -y nodejs
```

### 4. Install PM2
```bash
npm install -g pm2
```

### 5. Clone and run
```bash
cd /opt
git clone YOUR_REPO instaclaw
cd instaclaw
npm install
pm2 start server.js --name instaclaw
pm2 save
pm2 startup
```

### 6. Setup Nginx (for domain)
```bash
apt install nginx
```

Create /etc/nginx/sites-available/instaclaw:
```nginx
server {
    listen 80;
    server_name instaclaw.yourdomain.com;

    location / {
        proxy_pass http://localhost:3000;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
    }
}
```

```bash
ln -s /etc/nginx/sites-available/instaclaw /etc/nginx/sites-enabled/
nginx -t
systemctl reload nginx
```

### 7. SSL with Certbot
```bash
apt install certbot python3-certbot-nginx
certbot --nginx -d instaclaw.yourdomain.com
```

## Environment Variables
- `PORT` - Server port (default: 3000)

## Data
- `data.json` - All data stored here
- Back this up regularly!
