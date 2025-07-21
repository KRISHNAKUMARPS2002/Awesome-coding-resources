# 📘 Node.js App Deployment on VPS with Nginx, PM2, and SSL

This comprehensive guide covers deploying a Node.js application on a VPS using PM2 for process management, Nginx as a reverse proxy, and Let's Encrypt SSL for HTTPS security.

## 🔧 Prerequisites

- **VPS Server**: Ubuntu 20.04/22.04 (DigitalOcean, Hostinger, AWS, etc.)
- **Domain Name**: Pointed to your VPS IP address
- **Node.js Project**: Ready for deployment
- **SSH Access**: To your VPS server

## 📁 Recommended Project Structure

```
/root/projects/my-node-app/
├── app.js / server.js
├── package.json
├── package-lock.json
├── .env
├── .gitignore
└── ...other files
```

---

## 🚀 Initial Deployment Steps

### 1️⃣ Update System & Install Essentials

```bash
sudo apt update && sudo apt upgrade -y
sudo apt install nginx curl git ufw -y
```

### 2️⃣ Install Node.js & npm

```bash
# Install Node.js 18.x (LTS)
curl -fsSL https://deb.nodesource.com/setup_18.x | sudo -E bash -
sudo apt install -y nodejs

# Verify installation
node -v
npm -v
```

### 3️⃣ Clone Your Project

```bash
# Navigate to projects directory
cd /root/projects/

# Clone your repository
git clone https://github.com/yourusername/your-repo.git
cd your-repo

# Install dependencies
npm install

# Create/configure environment variables
nano .env
```

### 4️⃣ Install & Configure PM2

```bash
# Install PM2 globally
sudo npm install -g pm2

# Start your application
pm2 start app.js --name "my-node-app"

# Save PM2 configuration
pm2 save

# Setup PM2 to start on boot
pm2 startup
# Copy and run the command that PM2 outputs

# Check PM2 status
pm2 status
pm2 logs
```

### 5️⃣ Configure Nginx Reverse Proxy

```bash
# Create Nginx configuration
sudo nano /etc/nginx/sites-available/my-node-app
```

**Paste this configuration:**

```nginx
server {
    listen 80;
    server_name your-domain.com www.your-domain.com;

    location / {
        proxy_pass http://localhost:5000;  # Change to your Node.js port
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
        proxy_cache_bypass $http_upgrade;
    }

    # Optional: Serve static files directly
    location /public {
        alias /root/projects/my-node-app/public;
        expires 30d;
        add_header Cache-Control "public, immutable";
    }

    error_log /var/log/nginx/my-node-app_error.log;
    access_log /var/log/nginx/my-node-app_access.log;
}
```

**Enable the site:**

```bash
# Create symbolic link
sudo ln -s /etc/nginx/sites-available/my-node-app /etc/nginx/sites-enabled/

# Test Nginx configuration
sudo nginx -t

# Reload Nginx
sudo systemctl reload nginx
```

### 6️⃣ Configure Firewall

```bash
# Allow SSH, HTTP, and HTTPS
sudo ufw allow ssh
sudo ufw allow 'Nginx Full'
sudo ufw --force enable
sudo ufw status
```

### 7️⃣ Install SSL Certificate (Let's Encrypt)

```bash
# Install Certbot
sudo apt install certbot python3-certbot-nginx -y

# Obtain SSL certificate
sudo certbot --nginx -d your-domain.com -d www.your-domain.com

# Test automatic renewal
sudo certbot renew --dry-run
```

### 8️⃣ Final Verification

```bash
# Check if everything is running
pm2 status
sudo systemctl status nginx
sudo systemctl status certbot.timer

# Test your website
curl -I https://your-domain.com
```

---

## 🔄 Code Updates & Redeployment

### Method 1: Git Pull & Restart (Recommended)

```bash
# Navigate to your project directory
cd /root/projects/my-node-app

# Pull latest changes
git pull origin main  # or your main branch

# Install any new dependencies
npm install

# Restart the application with PM2
pm2 restart my-node-app

# Check logs for any errors
pm2 logs my-node-app --lines 50
```

### Method 2: Zero-Downtime Deployment

```bash
# Navigate to project directory
cd /root/projects/my-node-app

# Pull latest changes
git pull origin main

# Install dependencies
npm install

# Reload the application (zero downtime)
pm2 reload my-node-app

# Monitor the reload process
pm2 logs my-node-app --lines 20
```

### Method 3: Complete Redeployment

```bash
# Stop the application
pm2 stop my-node-app

# Pull latest changes
cd /root/projects/my-node-app
git pull origin main

# Clean install dependencies
rm -rf node_modules package-lock.json
npm install

# Start the application
pm2 start my-node-app

# Save PM2 configuration
pm2 save
```

### Automated Deployment Script

Create a deployment script for easier updates:

```bash
# Create deployment script
nano /root/deploy.sh
```

**Add this content:**

```bash
#!/bin/bash

APP_DIR="/root/projects/my-node-app"
APP_NAME="my-node-app"

echo "🚀 Starting deployment..."

# Navigate to app directory
cd $APP_DIR

# Pull latest changes
echo "📥 Pulling latest changes..."
git pull origin main

# Install dependencies
echo "📦 Installing dependencies..."
npm install

# Restart application
echo "🔄 Restarting application..."
pm2 restart $APP_NAME

# Show status
echo "✅ Deployment complete!"
pm2 status
pm2 logs $APP_NAME --lines 10
```

**Make it executable:**

```bash
chmod +x /root/deploy.sh

# Run deployment
./deploy.sh
```

---

## 🔍 Monitoring & Maintenance

### Check Application Status

```bash
# PM2 status
pm2 status
pm2 logs my-node-app
pm2 monit

# System resources
htop
df -h
free -m
```

### Check Running Ports

```bash
# Method 1: lsof
sudo lsof -i -P -n | grep LISTEN

# Method 2: netstat
sudo netstat -tuln

# Method 3: ss (recommended)
sudo ss -tuln
```

### Nginx Commands

```bash
# Test configuration
sudo nginx -t

# Reload configuration
sudo systemctl reload nginx

# Restart Nginx
sudo systemctl restart nginx

# Check status
sudo systemctl status nginx

# View logs
sudo tail -f /var/log/nginx/error.log
sudo tail -f /var/log/nginx/my-node-app_access.log
```

### SSL Certificate Management

```bash
# Check certificate status
sudo certbot certificates

# Manual renewal
sudo certbot renew

# Test renewal
sudo certbot renew --dry-run
```

---

## 🛠 Troubleshooting

### Common Issues & Solutions

**1. Port Already in Use**

```bash
# Find process using port
sudo lsof -i :5000
# Kill process if needed
sudo kill -9 <PID>
```

**2. PM2 App Won't Start**

```bash
# Check logs
pm2 logs my-node-app
# Delete and recreate
pm2 delete my-node-app
pm2 start app.js --name my-node-app
```

**3. Nginx 502 Bad Gateway**

```bash
# Check if Node.js app is running
pm2 status
# Check Nginx configuration
sudo nginx -t
# Check logs
sudo tail -f /var/log/nginx/error.log
```

**4. SSL Certificate Issues**

```bash
# Renew certificate
sudo certbot renew
# Check certificate expiry
sudo certbot certificates
```

---

## 📝 Important Notes

- **Backup**: Always backup your database and `.env` files before deployment
- **Environment Variables**: Ensure all environment variables are properly set
- **Database**: If using a database, make sure it's running and accessible
- **Logs**: Regularly check PM2 and Nginx logs for issues
- **Security**: Keep your system updated with `sudo apt update && sudo apt upgrade`
- **Monitoring**: Consider setting up monitoring tools like Uptime Robot

---

## 🔗 Useful Commands Cheat Sheet

```bash
# PM2 Commands
pm2 list                    # List all processes
pm2 restart <app-name>      # Restart specific app
pm2 reload <app-name>       # Zero-downtime restart
pm2 stop <app-name>         # Stop app
pm2 delete <app-name>       # Delete app
pm2 logs <app-name>         # View logs
pm2 flush                   # Clear all logs
pm2 save                    # Save current configuration
pm2 resurrect              # Restore saved configuration

# Git Commands
git status                  # Check repository status
git pull origin main        # Pull latest changes
git log --oneline -5        # View recent commits

# System Commands
sudo systemctl restart nginx    # Restart Nginx
sudo systemctl status nginx     # Check Nginx status
sudo ufw status                # Check firewall status
htop                           # System monitor
df -h                          # Disk usage
```

This guide should cover all your deployment and redeployment needs. Save it for future reference and modify the domain names, paths, and app names according to your specific project requirements.
