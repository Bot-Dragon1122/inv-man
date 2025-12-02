# Deployment Guide - Inventory Management System

This guide covers deploying the Inventory Management System to various platforms.

---

## Prerequisites

Before deploying, ensure you have:
- Python 3.11+
- PostgreSQL database (or use platform-provided databases)
- Git installed

---

## 1. Local Development (Localhost)

### Step 1: Clone the Repository
```bash
git clone <your-repo-url>
cd inventory-management
```

### Step 2: Create Virtual Environment
```bash
python -m venv venv
source venv/bin/activate  # Linux/Mac
# or
venv\Scripts\activate     # Windows
```

### Step 3: Install Dependencies
```bash
pip install flask flask-sqlalchemy flask-login gunicorn psycopg2-binary email-validator
```

### Step 4: Set Environment Variables
Create a `.env` file or export variables:
```bash
export DATABASE_URL="postgresql://user:password@localhost:5432/inventory_db"
export SESSION_SECRET="your-secret-key-here"
```

### Step 5: Set Up PostgreSQL Database
```bash
# Create database
psql -U postgres
CREATE DATABASE inventory_db;
\q
```

### Step 6: Run the Application
```bash
# Development
python -c "from app import app; app.run(host='0.0.0.0', port=5000, debug=True)"

# Production
gunicorn --bind 0.0.0.0:5000 main:app
```

### Step 7: Access the Application
Open http://localhost:5000 in your browser.
- Default login: `admin` / `admin123`

---

## 2. Render Deployment

### Step 1: Prepare Your Repository
Push your code to GitHub/GitLab.

### Step 2: Create a New Web Service
1. Go to [render.com](https://render.com) and sign up/login
2. Click "New +" → "Web Service"
3. Connect your GitHub repository

### Step 3: Configure the Service
- **Name**: `inventory-management`
- **Environment**: `Python 3`
- **Build Command**: `pip install -r requirements.txt`
- **Start Command**: `gunicorn --bind 0.0.0.0:$PORT main:app`

### Step 4: Create requirements.txt
Create this file in your project root:
```
flask>=3.0.0
flask-sqlalchemy>=3.1.0
flask-login>=0.6.0
gunicorn>=21.0.0
psycopg2-binary>=2.9.0
email-validator>=2.0.0
```

### Step 5: Add PostgreSQL Database
1. In Render dashboard, click "New +" → "PostgreSQL"
2. Create the database
3. Copy the "Internal Database URL"

### Step 6: Set Environment Variables
In your Web Service settings, add:
- `DATABASE_URL`: (paste your PostgreSQL URL)
- `SESSION_SECRET`: (generate a secure random string)
- `PYTHON_VERSION`: `3.11.0`

### Step 7: Deploy
Render will automatically deploy when you push to your connected branch.

---

## 3. Railway Deployment

### Step 1: Install Railway CLI (Optional)
```bash
npm install -g @railway/cli
railway login
```

### Step 2: Create New Project
1. Go to [railway.app](https://railway.app) and sign up/login
2. Click "New Project" → "Deploy from GitHub repo"
3. Select your repository

### Step 3: Add PostgreSQL
1. In your project, click "New" → "Database" → "Add PostgreSQL"
2. Railway automatically sets `DATABASE_URL`

### Step 4: Configure Environment Variables
In the "Variables" tab, add:
```
SESSION_SECRET=your-secure-secret-key
PORT=5000
```

### Step 5: Add Procfile
Create a `Procfile` in your project root:
```
web: gunicorn --bind 0.0.0.0:$PORT main:app
```

### Step 6: Add railway.json (Optional)
```json
{
  "$schema": "https://railway.app/railway.schema.json",
  "build": {
    "builder": "NIXPACKS"
  },
  "deploy": {
    "startCommand": "gunicorn --bind 0.0.0.0:$PORT main:app",
    "restartPolicyType": "ON_FAILURE",
    "restartPolicyMaxRetries": 10
  }
}
```

### Step 7: Deploy
Push to your connected branch, and Railway will automatically deploy.

---

## 4. GitHub (with GitHub Actions + Self-Hosted Runner)

GitHub itself doesn't host applications, but you can use GitHub Actions for CI/CD to deploy to your own server.

### Step 1: Create GitHub Actions Workflow
Create `.github/workflows/deploy.yml`:

```yaml
name: Deploy Inventory Management

on:
  push:
    branches: [main]

jobs:
  deploy:
    runs-on: ubuntu-latest
    
    steps:
    - uses: actions/checkout@v4
    
    - name: Set up Python
      uses: actions/setup-python@v5
      with:
        python-version: '3.11'
    
    - name: Install dependencies
      run: |
        python -m pip install --upgrade pip
        pip install -r requirements.txt
    
    - name: Run tests
      run: |
        # Add your tests here
        echo "Tests passed"
    
    - name: Deploy to Server
      uses: appleboy/ssh-action@v1.0.0
      with:
        host: ${{ secrets.SERVER_HOST }}
        username: ${{ secrets.SERVER_USER }}
        key: ${{ secrets.SSH_PRIVATE_KEY }}
        script: |
          cd /var/www/inventory-management
          git pull origin main
          source venv/bin/activate
          pip install -r requirements.txt
          sudo systemctl restart inventory-app
```

### Step 2: Set GitHub Secrets
In your repository settings → Secrets and variables → Actions:
- `SERVER_HOST`: Your server IP
- `SERVER_USER`: SSH username
- `SSH_PRIVATE_KEY`: Your private SSH key
- `DATABASE_URL`: Your PostgreSQL connection string
- `SESSION_SECRET`: Your secret key

### Step 3: Server Setup (VPS/Cloud Server)
On your server:
```bash
# Install dependencies
sudo apt update
sudo apt install python3.11 python3.11-venv postgresql nginx

# Clone repository
cd /var/www
git clone <your-repo-url> inventory-management
cd inventory-management

# Create virtual environment
python3.11 -m venv venv
source venv/bin/activate
pip install -r requirements.txt

# Create systemd service
sudo nano /etc/systemd/system/inventory-app.service
```

Add this content:
```ini
[Unit]
Description=Inventory Management System
After=network.target

[Service]
User=www-data
Group=www-data
WorkingDirectory=/var/www/inventory-management
Environment="DATABASE_URL=postgresql://user:pass@localhost/inventory_db"
Environment="SESSION_SECRET=your-secret-key"
ExecStart=/var/www/inventory-management/venv/bin/gunicorn --workers 3 --bind unix:inventory.sock main:app

[Install]
WantedBy=multi-user.target
```

Start the service:
```bash
sudo systemctl start inventory-app
sudo systemctl enable inventory-app
```

### Step 4: Configure Nginx
```nginx
server {
    listen 80;
    server_name yourdomain.com;

    location / {
        proxy_pass http://unix:/var/www/inventory-management/inventory.sock;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
    }
}
```

---

## Environment Variables Reference

| Variable | Description | Required |
|----------|-------------|----------|
| `DATABASE_URL` | PostgreSQL connection string | Yes |
| `SESSION_SECRET` | Flask session secret key | Yes |
| `PORT` | Application port (default: 5000) | Platform-dependent |

---

## Security Recommendations

1. **Change Default Credentials**: After first login, change the admin password
2. **Use Strong SESSION_SECRET**: Generate with `python -c "import secrets; print(secrets.token_hex(32))"`
3. **Enable HTTPS**: Use SSL certificates (Let's Encrypt is free)
4. **Database Backups**: Set up regular PostgreSQL backups
5. **Update Dependencies**: Regularly update Python packages

---

## Troubleshooting

### Database Connection Issues
- Verify `DATABASE_URL` format: `postgresql://user:password@host:port/database`
- Check if PostgreSQL is running
- Ensure database user has proper permissions

### Application Won't Start
- Check logs: `gunicorn --log-level debug main:app`
- Verify all environment variables are set
- Ensure port is not in use

### Static Files Not Loading
- For production, consider using WhiteNoise or serving via Nginx
- Check file permissions

---

## Quick Start Commands

```bash
# Create requirements.txt
pip freeze > requirements.txt

# Generate secret key
python -c "import secrets; print(secrets.token_hex(32))"

# Test database connection
python -c "from app import db, app; app.app_context().push(); db.create_all(); print('OK')"
```
