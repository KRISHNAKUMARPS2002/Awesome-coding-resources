# 🚀 Django Project Deployment Guide (Production-Ready)

This guide explains how to deploy a **Django project** on a Linux-based VPS (e.g., Hostinger, DigitalOcean, AWS EC2) using:

- **Gunicorn** as WSGI server
- **Nginx** as reverse proxy
- **Certbot** for free HTTPS SSL
- **Systemd** to manage services
- **venv** for Python virtual environment

---

## 🌐 Live URL

🔗 https://yourdomain.com (Replace with your actual domain)

---

## 🧱 Tech Stack

- **Framework**: Django (Python)
- **OS**: Ubuntu 20.04+ / Debian
- **Web Server**: Nginx
- **App Server**: Gunicorn
- **SSL**: Let's Encrypt (Certbot)
- **Database**: SQLite / PostgreSQL
- **Process Manager**: systemd
- **Firewall**: ufw

---

## 📁 Folder Structure (Example)

```
/var/www/yourproject/
├── yourproject/          # Django project (wsgi.py lives here)
├── manage.py
├── requirements.txt
├── venv/                 # Python virtual environment
└── static/               # Collected static files
```

---

## 🔧 Initial Deployment Steps

### 1️⃣ SSH into Your VPS

```bash
ssh root@your-server-ip
```

### 2️⃣ Update System & Install Dependencies

```bash
sudo apt update && sudo apt upgrade -y
sudo apt install python3-pip python3-dev libpq-dev nginx curl git -y
```

### 3️⃣ Clone Your Project

```bash
cd /var/www/
git clone https://github.com/yourusername/yourproject.git
cd yourproject
```

### 4️⃣ Set Up Virtual Environment

```bash
python3 -m venv venv
source venv/bin/activate
pip install --upgrade pip
pip install -r requirements.txt
```

### 5️⃣ Configure Django Settings

```Check Port Availability 🔍

Check All Listening Ports:  sudo netstat -tuln

Check Specific Port (e.g., 8001):  sudo lsof -i :8001

🚀 Run Gunicorn on Available Port
gunicorn --bind 127.0.0.1:8001 yourproject.wsgi:application

Replace yourproject with your Django project folder that contains wsgi.py
```

```python
# In settings.py
DEBUG = False
ALLOWED_HOSTS = ['yourdomain.com', 'www.yourdomain.com', 'your-server-ip']

# Static files
STATIC_ROOT = os.path.join(BASE_DIR, 'static/')
```

### 6️⃣ Prepare Database & Static Files

```bash
python manage.py migrate
python manage.py collectstatic --noinput
```

### 7️⃣ Create Gunicorn Service

```bash
sudo nano /etc/systemd/system/gunicorn.service
```

```ini
[Unit]
Description=Gunicorn daemon for Django project
After=network.target

[Service]
User=root
Group=www-data
WorkingDirectory=/var/www/yourproject
ExecStart=/var/www/yourproject/venv/bin/gunicorn --workers 3 --bind 127.0.0.1:8001 yourproject.wsgi:application

[Install]
WantedBy=multi-user.target
```

### 8️⃣ Start & Enable Gunicorn

```bash
sudo systemctl daemon-reload
sudo systemctl start gunicorn
sudo systemctl enable gunicorn
sudo systemctl status gunicorn  # Check status
```

### 9️⃣ Configure Nginx

```bash
sudo nano /etc/nginx/sites-available/yourproject
```

```nginx
server {
    listen 80;
    server_name yourdomain.com www.yourdomain.com;

    location = /favicon.ico {
        access_log off;
        log_not_found off;
    }

    location /static/ {
        root /var/www/yourproject;
    }

    location /media/ {
        root /var/www/yourproject;
    }

    location / {
        include proxy_params;
        proxy_pass http://127.0.0.1:8001;
    }
}
```

### 🔟 Enable Nginx Site

```bash
sudo ln -s /etc/nginx/sites-available/yourproject /etc/nginx/sites-enabled
sudo nginx -t  # Test configuration
sudo systemctl restart nginx
```

### 1️⃣1️⃣ Setup SSL with Certbot

```bash
sudo apt install certbot python3-certbot-nginx -y
sudo certbot --nginx -d yourdomain.com -d www.yourdomain.com
```

### 1️⃣2️⃣ Configure Firewall (Optional)

```bash
sudo ufw allow 'Nginx Full'
sudo ufw allow OpenSSH
sudo ufw enable
```

### 1️⃣3️⃣ Verify Auto-Renewal

```bash
sudo systemctl status certbot.timer
sudo certbot renew --dry-run
```

---

## 🔁 Redeployment Steps (After git pull)

After making changes locally and pushing to GitHub:

```bash
# SSH into server
ssh root@your-server-ip

# Navigate to project
cd /var/www/yourproject

# Pull latest code
git pull origin main

# Activate virtualenv
source venv/bin/activate

# Install new dependencies (if any)
pip install -r requirements.txt

# Run migrations (if needed)
python manage.py migrate

# Collect static files (if changed)
python manage.py collectstatic --noinput

# Restart Gunicorn
sudo systemctl restart gunicorn

# Optional: Restart Nginx if config changed
sudo systemctl restart nginx
```

---

## 🔍 Troubleshooting Commands

```bash
# Check Gunicorn status
sudo systemctl status gunicorn

# View Gunicorn logs
sudo journalctl -u gunicorn

# Check Nginx status
sudo systemctl status nginx

# View Nginx error logs
sudo tail -f /var/log/nginx/error.log

# Test Nginx configuration
sudo nginx -t

# Check if port 8001 is being used
sudo netstat -tulpn | grep :8001

# Restart all services
sudo systemctl restart gunicorn nginx
```

---

## 📝 Important Notes

- Replace `yourproject` with your actual project name
- Replace `yourdomain.com` with your actual domain
- Update `ALLOWED_HOSTS` in Django settings
- Ensure your domain's DNS points to your server IP
- Keep your `requirements.txt` updated
- Consider using PostgreSQL for production instead of SQLite
- Set up regular backups for your database and media files

---

## 🚨 Security Considerations

- Never commit sensitive data (API keys, passwords) to Git
- Use environment variables for sensitive settings
- Keep your system and packages updated
- Consider using a non-root user for deployment
- Set up proper logging and monitoring
- Use strong passwords and SSH keys

---

## 📚 Additional Resources

- [Django Deployment Checklist](https://docs.djangoproject.com/en/stable/howto/deployment/checklist/)
- [Gunicorn Documentation](https://docs.gunicorn.org/)
- [Nginx Documentation](https://nginx.org/en/docs/)
- [Let's Encrypt Documentation](https://letsencrypt.org/docs/)

---

**Happy Deploying! 🎉**
