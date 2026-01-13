# Installation Guide: Digital Ocean Deployment from GitHub

**Author:** VasilMihovCom

This guide provides complete step-by-step instructions for deploying the Financial Education Portal to a Digital Ocean droplet directly from GitHub.

---

## Table of Contents

1. [Prerequisites](#prerequisites)
2. [Digital Ocean Droplet Setup](#digital-ocean-droplet-setup)
3. [Initial Server Configuration](#initial-server-configuration)
4. [Install Required Software](#install-required-software)
5. [Clone and Configure Application](#clone-and-configure-application)
6. [Supabase Setup](#supabase-setup)
7. [Build and Deploy](#build-and-deploy)
8. [Nginx Configuration](#nginx-configuration)
9. [SSL Certificate Setup](#ssl-certificate-setup)
10. [Process Management with PM2](#process-management-with-pm2)
11. [Post-Deployment](#post-deployment)
12. [Troubleshooting](#troubleshooting)

---

## Prerequisites

Before you begin, ensure you have:

- **Digital Ocean Account** - Sign up at [digitalocean.com](https://www.digitalocean.com)
- **GitHub Account** - With access to your repository
- **Domain Name** (optional but recommended) - For custom domain setup
- **Supabase Account** - Free tier available at [supabase.com](https://supabase.com)
- **SSH Key** - For secure server access
- **Basic Terminal Knowledge** - Familiarity with command line

### Minimum Server Requirements

- **RAM:** 2GB minimum (4GB recommended for production)
- **CPU:** 1 vCPU minimum (2 vCPUs recommended)
- **Storage:** 25GB SSD
- **Bandwidth:** 2TB transfer
- **Operating System:** Ubuntu 22.04 LTS

---

## Digital Ocean Droplet Setup

### Step 1: Create SSH Key (if you don't have one)

On your local machine:

```bash
# Generate SSH key pair
ssh-keygen -t ed25519 -C "your_email@example.com"

# Display your public key
cat ~/.ssh/id_ed25519.pub
```

Copy the output to add to Digital Ocean.

### Step 2: Create Droplet

1. Log into Digital Ocean Dashboard
2. Click **"Create"** → **"Droplets"**
3. Choose configuration:
   - **Image:** Ubuntu 22.04 LTS
   - **Plan:** Basic ($12/month - 2GB RAM, 1 vCPU)
   - **Datacenter:** Choose closest to your audience
   - **Authentication:** SSH Key (paste your public key)
   - **Hostname:** `financial-portal` (or your choice)
4. Click **"Create Droplet"**
5. Wait for droplet creation (1-2 minutes)
6. Note your droplet's IP address (e.g., `123.456.789.012`)

### Step 3: Initial SSH Connection

```bash
# Connect to your droplet
ssh root@YOUR_DROPLET_IP

# You should see Ubuntu welcome message
```

---

## Initial Server Configuration

### Step 1: Update System Packages

```bash
# Update package list
apt update

# Upgrade all packages
apt upgrade -y

# Install essential tools
apt install -y curl wget git build-essential
```

### Step 2: Create Non-Root User (Security Best Practice)

```bash
# Create new user
adduser deployer

# Add to sudo group
usermod -aG sudo deployer

# Copy SSH keys to new user
rsync --archive --chown=deployer:deployer ~/.ssh /home/deployer
```

### Step 3: Configure Firewall

```bash
# Enable UFW firewall
ufw allow OpenSSH
ufw allow 80/tcp    # HTTP
ufw allow 443/tcp   # HTTPS
ufw enable

# Verify firewall status
ufw status
```

### Step 4: Switch to Deployer User

```bash
# Switch user
su - deployer

# Or reconnect via SSH
# ssh deployer@YOUR_DROPLET_IP
```

---

## Install Required Software

### Step 1: Install Node.js 18 LTS

```bash
# Download and run Node.js setup script
curl -fsSL https://deb.nodesource.com/setup_18.x | sudo -E bash -

# Install Node.js and npm
sudo apt install -y nodejs

# Verify installation
node --version  # Should show v18.x.x
npm --version   # Should show 9.x.x or higher
```

### Step 2: Install PM2 Process Manager

```bash
# Install PM2 globally
sudo npm install -g pm2

# Verify installation
pm2 --version

# Set PM2 to start on boot
pm2 startup
# Copy and run the command it outputs
```

### Step 3: Install Nginx Web Server

```bash
# Install Nginx
sudo apt install -y nginx

# Start Nginx
sudo systemctl start nginx
sudo systemctl enable nginx

# Verify Nginx is running
sudo systemctl status nginx

# Test: Visit http://YOUR_DROPLET_IP in browser
# You should see Nginx welcome page
```

---

## Clone and Configure Application

### Step 1: Clone Repository from GitHub

```bash
# Navigate to home directory
cd ~

# Clone your repository
git clone https://github.com/YOUR_USERNAME/financial-education-portal.git

# Navigate into project
cd financial-education-portal
```

If your repository is private, you'll need to set up authentication:

```bash
# Option 1: HTTPS with Personal Access Token
git clone https://YOUR_TOKEN@github.com/YOUR_USERNAME/financial-education-portal.git

# Option 2: SSH (recommended)
# First, add SSH key to GitHub account
ssh-keygen -t ed25519 -C "deployer@server"
cat ~/.ssh/id_ed25519.pub
# Add this key to GitHub → Settings → SSH Keys
git clone git@github.com:YOUR_USERNAME/financial-education-portal.git
```

### Step 2: Install Dependencies

```bash
# Install npm packages
npm install

# This may take 2-5 minutes
```

---

## Supabase Setup

### Step 1: Create Supabase Project

1. Go to [supabase.com](https://supabase.com)
2. Click **"Start your project"** or **"New Project"**
3. Enter project details:
   - **Name:** financial-education-portal
   - **Database Password:** Generate strong password
   - **Region:** Choose closest to droplet
4. Wait for project creation (2-3 minutes)

### Step 2: Get Supabase Credentials

1. In Supabase Dashboard, go to **Settings** → **API**
2. Copy the following:
   - **Project URL** (e.g., `https://xxxxx.supabase.co`)
   - **anon/public key** (long string starting with `eyJ...`)

### Step 3: Configure Environment Variables

```bash
# Create .env file
nano .env
```

Add the following content:

```env
VITE_SUPABASE_URL=https://YOUR_PROJECT_ID.supabase.co
VITE_SUPABASE_ANON_KEY=your_anon_key_here
```

Save and exit (`Ctrl+X`, then `Y`, then `Enter`)

### Step 4: Run Database Migrations

There are two ways to run migrations:

**Option A: Via Supabase Dashboard (Recommended)**

1. In Supabase Dashboard, go to **Database** → **Migrations**
2. Click **"New migration"**
3. Copy content from each file in `supabase/migrations/` folder
4. Paste and run migrations in order:
   - `20251204175752_create_initial_schema.sql`
   - `20251204190515_transform_to_financial_education_portal.sql`
   - `20251205054803_transform_to_educational_portal_v2.sql`
   - `20251205162834_create_tracking_scripts_table.sql`
   - `20251214081313_create_lead_gen_system.sql`
   - `20251214103621_add_dual_iframe_and_visitor_detection.sql`
   - `20251214122218_add_enhanced_visitor_detection.sql`

**Option B: Via Supabase CLI**

```bash
# Install Supabase CLI
npm install -g supabase

# Login to Supabase
supabase login

# Link to your project
supabase link --project-ref YOUR_PROJECT_ID

# Push migrations
supabase db push
```

### Step 5: Deploy Edge Functions

```bash
# Deploy visitor detection function
supabase functions deploy visitor-detection

# Verify deployment
supabase functions list
```

Or use the Supabase Dashboard:
1. Go to **Edge Functions**
2. Click **"Deploy new function"**
3. Copy content from `supabase/functions/visitor-detection/index.ts`
4. Deploy

### Step 6: Create Admin User

```bash
# In Supabase Dashboard, go to Authentication → Users
# Click "Add user" and create admin account with email/password
```

---

## Build and Deploy

### Step 1: Build Production Bundle

```bash
# Navigate to project directory
cd ~/financial-education-portal

# Run production build
npm run build

# This creates a 'dist' folder with optimized files
# Build should complete in 30-60 seconds
```

### Step 2: Verify Build

```bash
# Check dist folder exists
ls -la dist/

# Preview build locally (optional)
npm run preview
```

---

## Nginx Configuration

### Step 1: Create Nginx Configuration

```bash
# Create new site configuration
sudo nano /etc/nginx/sites-available/financial-portal
```

Add the following configuration:

```nginx
server {
    listen 80;
    server_name YOUR_DOMAIN.com www.YOUR_DOMAIN.com;
    # Or use: server_name YOUR_DROPLET_IP;

    root /home/deployer/financial-education-portal/dist;
    index index.html;

    # Gzip compression
    gzip on;
    gzip_types text/plain text/css application/json application/javascript text/xml application/xml application/xml+rss text/javascript;

    # Security headers
    add_header X-Frame-Options "SAMEORIGIN" always;
    add_header X-Content-Type-Options "nosniff" always;
    add_header X-XSS-Protection "1; mode=block" always;

    # Main location
    location / {
        try_files $uri $uri/ /index.html;
    }

    # Cache static assets
    location ~* \.(js|css|png|jpg|jpeg|gif|ico|svg|woff|woff2|ttf|eot)$ {
        expires 1y;
        add_header Cache-Control "public, immutable";
    }

    # Deny access to hidden files
    location ~ /\. {
        deny all;
    }
}
```

Save and exit.

### Step 2: Enable Site

```bash
# Create symbolic link
sudo ln -s /etc/nginx/sites-available/financial-portal /etc/nginx/sites-enabled/

# Remove default site
sudo rm /etc/nginx/sites-enabled/default

# Test configuration
sudo nginx -t

# Reload Nginx
sudo systemctl reload nginx
```

### Step 3: Test Deployment

Visit `http://YOUR_DROPLET_IP` or `http://YOUR_DOMAIN.com` in your browser. You should see the application running!

---

## SSL Certificate Setup

### Step 1: Install Certbot

```bash
# Install Certbot and Nginx plugin
sudo apt install -y certbot python3-certbot-nginx
```

### Step 2: Obtain SSL Certificate

**Prerequisites:** Domain must be pointing to your droplet IP (A record)

```bash
# Obtain and install certificate
sudo certbot --nginx -d YOUR_DOMAIN.com -d www.YOUR_DOMAIN.com

# Follow prompts:
# - Enter email address
# - Agree to terms
# - Choose redirect HTTP to HTTPS (option 2)
```

### Step 3: Test Auto-Renewal

```bash
# Test renewal process
sudo certbot renew --dry-run

# Certbot will auto-renew before expiration
```

### Step 4: Verify HTTPS

Visit `https://YOUR_DOMAIN.com` - you should see a secure lock icon!

---

## Process Management with PM2

While this is a static site, you can use PM2 to manage preview or development servers:

### Step 1: Create PM2 Ecosystem File

```bash
# Create ecosystem file
nano ecosystem.config.js
```

Add content:

```javascript
module.exports = {
  apps: [{
    name: 'financial-portal-preview',
    script: 'npm',
    args: 'run preview',
    cwd: '/home/deployer/financial-education-portal',
    instances: 1,
    autorestart: true,
    watch: false,
    max_memory_restart: '500M',
    env: {
      NODE_ENV: 'production',
      PORT: 4173
    }
  }]
};
```

### Step 2: Start with PM2 (Optional)

```bash
# Start application
pm2 start ecosystem.config.js

# Save PM2 process list
pm2 save

# View logs
pm2 logs financial-portal-preview

# Monitor
pm2 monit
```

---

## Post-Deployment

### Step 1: Configure Domain DNS

If using a custom domain:

1. Go to your domain registrar
2. Add A records:
   ```
   Type: A
   Name: @
   Value: YOUR_DROPLET_IP

   Type: A
   Name: www
   Value: YOUR_DROPLET_IP
   ```
3. Wait for DNS propagation (5 minutes to 48 hours)

### Step 2: Set Up Monitoring

```bash
# Create monitoring script
nano ~/monitor.sh
```

Add content:

```bash
#!/bin/bash
if ! curl -f http://localhost &> /dev/null; then
    echo "Site is down!" | mail -s "Site Alert" your@email.com
    sudo systemctl restart nginx
fi
```

```bash
# Make executable
chmod +x ~/monitor.sh

# Add to crontab (runs every 5 minutes)
crontab -e
```

Add line:

```
*/5 * * * * /home/deployer/monitor.sh
```

### Step 3: Configure Backups

```bash
# Create backup script
nano ~/backup.sh
```

Add content:

```bash
#!/bin/bash
BACKUP_DIR="/home/deployer/backups"
DATE=$(date +%Y%m%d_%H%M%S)

mkdir -p $BACKUP_DIR

# Backup application files
tar -czf $BACKUP_DIR/app_$DATE.tar.gz /home/deployer/financial-education-portal

# Keep only last 7 backups
ls -t $BACKUP_DIR/app_*.tar.gz | tail -n +8 | xargs rm -f
```

```bash
# Make executable
chmod +x ~/backup.sh

# Schedule daily backups at 2 AM
crontab -e
```

Add line:

```
0 2 * * * /home/deployer/backup.sh
```

### Step 4: Update Workflow

When you need to deploy updates:

```bash
# Navigate to project
cd ~/financial-education-portal

# Pull latest changes
git pull origin main

# Install any new dependencies
npm install

# Rebuild application
npm run build

# Nginx will automatically serve new files
```

Create an update script for convenience:

```bash
# Create update script
nano ~/update-site.sh
```

Add content:

```bash
#!/bin/bash
cd /home/deployer/financial-education-portal
git pull origin main
npm install
npm run build
echo "Site updated successfully!"
```

```bash
# Make executable
chmod +x ~/update-site.sh

# Run updates with: ~/update-site.sh
```

---

## Troubleshooting

### Site Not Loading

**Check Nginx:**
```bash
sudo systemctl status nginx
sudo nginx -t
sudo tail -f /var/log/nginx/error.log
```

**Check Firewall:**
```bash
sudo ufw status
```

### Build Errors

**Check Node Version:**
```bash
node --version  # Should be 18.x
npm --version
```

**Clear and Rebuild:**
```bash
rm -rf node_modules dist
npm install
npm run build
```

### Supabase Connection Issues

**Check Environment Variables:**
```bash
cat .env
# Verify VITE_SUPABASE_URL and VITE_SUPABASE_ANON_KEY are correct
```

**Test Supabase Connection:**
```bash
curl https://YOUR_PROJECT_ID.supabase.co/rest/v1/
```

### SSL Certificate Issues

**Check Certificate Status:**
```bash
sudo certbot certificates
```

**Renew Manually:**
```bash
sudo certbot renew --force-renewal
```

### Permission Errors

**Fix Ownership:**
```bash
sudo chown -R deployer:deployer /home/deployer/financial-education-portal
```

---

## Performance Optimization

### Enable Nginx Caching

Edit Nginx configuration:

```bash
sudo nano /etc/nginx/sites-available/financial-portal
```

Add before `server` block:

```nginx
# Cache settings
proxy_cache_path /var/cache/nginx levels=1:2 keys_zone=my_cache:10m max_size=1g inactive=60m use_temp_path=off;
```

### Enable HTTP/2

In SSL configuration (after running certbot), Nginx automatically enables HTTP/2.

Verify:
```bash
curl -I --http2 https://YOUR_DOMAIN.com
```

### Database Query Optimization

1. In Supabase Dashboard, check **Database → Performance**
2. Add indexes for slow queries
3. Monitor query execution times

---

## Security Checklist

- [ ] Firewall enabled and configured
- [ ] Non-root user created
- [ ] SSH key authentication enabled
- [ ] Password authentication disabled
- [ ] Nginx security headers configured
- [ ] SSL certificate installed
- [ ] Environment variables secured
- [ ] Regular backups configured
- [ ] Monitoring enabled
- [ ] Database RLS policies active
- [ ] Supabase API keys rotated (if needed)

---

## Maintenance Schedule

**Daily:**
- Monitor logs for errors
- Check site availability

**Weekly:**
- Review analytics and errors
- Update content as needed
- Check disk space: `df -h`

**Monthly:**
- Update system packages: `sudo apt update && sudo apt upgrade`
- Review and rotate logs
- Test backup restoration
- Review SSL certificate expiration

**Quarterly:**
- Update Node.js if new LTS version
- Review and update dependencies
- Security audit
- Performance review

---

## Additional Resources

- [Digital Ocean Tutorials](https://www.digitalocean.com/community/tutorials)
- [Nginx Documentation](https://nginx.org/en/docs/)
- [Supabase Documentation](https://supabase.com/docs)
- [PM2 Documentation](https://pm2.keymetrics.io/docs)
- [Let's Encrypt Documentation](https://letsencrypt.org/docs/)

---

## Support

For deployment issues or questions:

1. Check the [TROUBLESHOOTING.md](./TROUBLESHOOTING.md) guide
2. Review [Digital Ocean Community](https://www.digitalocean.com/community)
3. Consult [Supabase Discord](https://discord.supabase.com)

---

**Deployment Guide by VasilMihovCom**

*Last Updated: December 2024*
