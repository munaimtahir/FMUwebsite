# FMU Website – Drupal Project

This is the official website codebase for **Faisalabad Medical University (FMU)**, built on [Drupal](https://www.drupal.org/). This repository uses the `drupal/recommended-project` Composer template to manage Drupal core, contributed modules, and dependencies.

---

## 🚀 Deployment Guide for VPS Server

Follow these steps to deploy the FMU website on your VPS server.

### Server Requirements

Your VPS server must have:
- **PHP 8.1+** (8.3 recommended) with extensions: `gd`, `curl`, `mbstring`, `xml`, `zip`, `pdo_mysql`
- **Composer 2.x** (PHP dependency manager)
- **MySQL 8.0+** or **MariaDB 10.6+** database
- **Apache 2.4+** (with mod_rewrite) or **Nginx**
- **SSH access** to your server

### Step 1: Connect to Your VPS

```bash
ssh your-username@your-server-ip
```

### Step 2: Download the Website Files

Navigate to where you want to install the website (commonly `/var/www/`):

```bash
cd /var/www
git clone https://github.com/munaimtahir/FMUwebsite.git
cd FMUwebsite
```

### Step 3: Install PHP Dependencies

```bash
composer install --no-dev --optimize-autoloader
```

This downloads Drupal core and all required libraries.

### Step 4: Create the Database

Log into MySQL and create a database for Drupal:

```bash
mysql -u root -p
```

Then run these SQL commands (replace `your_password` with a **strong, unique password** – at least 12 characters with letters, numbers, and symbols):

```sql
CREATE DATABASE fmu_drupal CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
CREATE USER 'fmu_user'@'localhost' IDENTIFIED BY 'your_password';
GRANT ALL PRIVILEGES ON fmu_drupal.* TO 'fmu_user'@'localhost';
FLUSH PRIVILEGES;
EXIT;
```

**⚠️ Important**: Replace `your_password` with your own secure password!

**Save these credentials** – you'll need them during Drupal installation:
- Database name: `fmu_drupal`
- Database user: `fmu_user`
- Database password: `your_password`

### Step 5: Set File Permissions

```bash
# Create the files directory for uploads
mkdir -p web/sites/default/files
chmod -R 755 web/sites/default/files
chown -R www-data:www-data web/sites/default/files

# Copy the default settings file
cp web/sites/default/default.settings.php web/sites/default/settings.php
chmod 644 web/sites/default/settings.php
```

### Step 6: Configure Apache Virtual Host

Create a virtual host configuration:

```bash
sudo nano /etc/apache2/sites-available/fmu.conf
```

Add the following (replace `fmu.edu.pk` with your actual domain):

```apache
<VirtualHost *:80>
    ServerName fmu.edu.pk
    ServerAlias www.fmu.edu.pk
    DocumentRoot /var/www/FMUwebsite/web
    
    <Directory /var/www/FMUwebsite/web>
        AllowOverride All
        Require all granted
    </Directory>
    
    ErrorLog ${APACHE_LOG_DIR}/fmu_error.log
    CustomLog ${APACHE_LOG_DIR}/fmu_access.log combined
</VirtualHost>
```

Enable the site and required modules:

```bash
sudo a2ensite fmu.conf
sudo a2enmod rewrite
sudo systemctl restart apache2
```

### Step 7: Run Drupal Installer

1. Open your browser and go to: `http://your-domain/`
2. Drupal will automatically redirect to the installation wizard
3. Select **Standard** installation profile
4. Enter your database credentials from Step 4
5. Set your site name as **Faisalabad Medical University**
6. Create your admin account (save these credentials securely!)

### Step 8: Secure Your Installation (Important!)

After installation, secure the settings file:

```bash
chmod 444 web/sites/default/settings.php
```

### Step 9: Enable HTTPS (Recommended for Production)

For a production website, you should enable HTTPS using Let's Encrypt:

```bash
sudo apt install certbot python3-certbot-apache
sudo certbot --apache -d fmu.edu.pk -d www.fmu.edu.pk
```

This will automatically configure SSL certificates and redirect HTTP to HTTPS.

---

## 📋 Information I Need From You

To help you further, please provide:

1. **Domain name**: What is your website domain? (e.g., fmu.edu.pk)
2. **VPS provider**: Who is your hosting provider? (e.g., DigitalOcean, AWS, Hostinger)
3. **Server OS**: What operating system is on your VPS? (e.g., Ubuntu 22.04)
4. **Content requirements**: What pages/sections do you need?
   - Departments listing
   - Faculty profiles
   - News/Notices
   - Admissions information
   - Contact page
   - Others?
5. **Existing content**: Do you have any existing content, images, or branding?

---

## 🔧 What's Already Set Up

This repository includes:

- ✅ **Drupal 11.2.8** (latest stable version)
- ✅ **Composer configuration** for dependency management
- ✅ **Security best practices** (credentials not in code)
- ✅ **Configuration sync directory** for future updates
- ✅ **Proper .gitignore** to keep the repository clean

---

## Quick Reference

### Prerequisites

- **PHP**: 8.1 or higher (8.3+ recommended)
- **Composer**: 2.x
- **Database**: MySQL 8.0+ / MariaDB 10.6+ / PostgreSQL 13+
- **Web Server**: Apache 2.4+ with mod_rewrite, or Nginx 1.x

## Project Structure

```
FMUwebsite/
├── composer.json       # Composer dependencies and scripts
├── composer.lock       # Locked dependency versions
├── config/
│   └── sync/          # Drupal configuration export directory
├── recipes/           # Drupal recipes (if any)
├── vendor/            # Composer dependencies (not committed)
├── web/               # Drupal web root
│   ├── core/          # Drupal core (not committed)
│   ├── modules/       # Custom and contrib modules
│   ├── themes/        # Custom and contrib themes
│   ├── profiles/      # Installation profiles
│   └── sites/         # Site-specific files and settings
└── README.md          # This file
```

## Important Notes

- **No credentials are committed**: Database credentials, API keys, and other secrets should never be committed to version control. Use environment-specific `settings.php` or `settings.local.php` files.
- **`vendor/` is not committed**: Dependencies are managed by Composer and must be installed on each environment using `composer install`.
- **Uploaded files are not committed**: The `web/sites/*/files/` directories contain user uploads and are environment-specific.

## Need Help?

If you encounter any issues during deployment, reply to this PR with:
1. The error message you see
2. Which step you were on
3. Your server details (OS, PHP version)

I'll help you troubleshoot!

---

## License

This project is for Faisalabad Medical University. See LICENSE file for details (if applicable).
