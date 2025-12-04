# FMU Website – Drupal Project

This is the official website codebase for **Faisalabad Medical University (FMU)**, built on [Drupal](https://www.drupal.org/). This repository uses the `drupal/recommended-project` Composer template to manage Drupal core, contributed modules, and dependencies.

---

## 🎯 Project Configuration

| Setting | Value |
|---------|-------|
| **Domain** | fmu.edu.pk |
| **Server** | Google Cloud VPS |
| **OS** | Ubuntu 22.04 |
| **Reference Site** | pmc.edu.pk |

---

## 🏛️ Website Sections (To Be Created)

Based on standard university website requirements:

### Main Pages
- **Home** – Hero banner, announcements, quick links
- **About FMU** – History, mission, vision, leadership
- **Academics** – Programs, courses, academic calendar

### Departments & Faculties
- Medicine
- Surgery
- Allied Health Sciences
- Nursing
- Basic Medical Sciences
- Dental (if applicable)

### Admissions
- Undergraduate admissions
- Postgraduate admissions
- Admission requirements
- Fee structure
- Important dates

### Students
- Student portal
- Examination schedule
- Results
- Student affairs

### Research
- Research publications
- Research centers
- Ongoing projects

### News & Events
- Latest news
- Upcoming events
- Notices/Announcements

### Contact
- Contact information
- Campus map
- Feedback form

---

## 🚀 Deployment Guide for Google Cloud VPS (Ubuntu 22.04)

Follow these steps to deploy the FMU website on your Google Cloud VPS.

### Server Requirements

Your Google Cloud VPS should have:
- **PHP 8.1+** (8.3 recommended) with extensions: `gd`, `curl`, `mbstring`, `xml`, `zip`, `pdo_mysql`
- **Composer 2.x** (PHP dependency manager)
- **MySQL 8.0+** or **MariaDB 10.6+** database
- **Apache 2.4+** (with mod_rewrite) or **Nginx**
- **SSH access** to your server

### Step 1: Connect to Your Google Cloud VPS

From Google Cloud Console, get your external IP address, then:

```bash
# Using gcloud CLI (recommended)
gcloud compute ssh your-instance-name --zone=your-zone

# Or using standard SSH
ssh your-username@YOUR_EXTERNAL_IP
```

### Step 2: Install Required Software (If Not Already Installed)

```bash
# Update system packages
sudo apt update && sudo apt upgrade -y

# Install Apache, PHP, and required extensions
sudo apt install -y apache2 php8.1 php8.1-cli php8.1-common php8.1-mysql \
    php8.1-zip php8.1-gd php8.1-mbstring php8.1-curl php8.1-xml php8.1-bcmath \
    php8.1-opcache libapache2-mod-php8.1

# Install MySQL
sudo apt install -y mysql-server

# Secure MySQL installation (IMPORTANT!)
sudo mysql_secure_installation

# Install Composer (from Ubuntu repository)
sudo apt install -y composer

# Install Git
sudo apt install -y git

# Enable Apache modules
sudo a2enmod rewrite
sudo systemctl restart apache2
```

### Step 3: Download the Website Files

Navigate to where you want to install the website:

```bash
cd /var/www
sudo git clone https://github.com/munaimtahir/FMUwebsite.git
cd FMUwebsite
sudo chown -R $USER:www-data .
```

### Step 4: Install PHP Dependencies

```bash
composer install --no-dev --optimize-autoloader
```

This downloads Drupal core and all required libraries.

### Step 5: Create the Database

Log into MySQL and create a database for Drupal:

```bash
sudo mysql
```

Then run these SQL commands (replace `YOUR_SECURE_PASSWORD_HERE` with a **strong, unique password** – at least 12 characters with letters, numbers, and symbols):

```sql
CREATE DATABASE fmu_drupal CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
CREATE USER 'fmu_user'@'localhost' IDENTIFIED BY 'YOUR_SECURE_PASSWORD_HERE';
GRANT ALL PRIVILEGES ON fmu_drupal.* TO 'fmu_user'@'localhost';
FLUSH PRIVILEGES;
EXIT;
```

**⚠️ Important**: Replace `YOUR_SECURE_PASSWORD_HERE` with your own secure password!

**Save these credentials** – you'll need them during Drupal installation:
- Database name: `fmu_drupal`
- Database user: `fmu_user`
- Database password: (your chosen password)

### Step 6: Set File Permissions

```bash
# Create the files directory for uploads
mkdir -p web/sites/default/files
chmod -R 755 web/sites/default/files
sudo chown -R www-data:www-data web/sites/default/files

# Copy the default settings file
cp web/sites/default/default.settings.php web/sites/default/settings.php
chmod 644 web/sites/default/settings.php
```

### Step 7: Configure Apache Virtual Host for fmu.edu.pk

Create a virtual host configuration:

```bash
sudo nano /etc/apache2/sites-available/fmu.edu.pk.conf
```

Add the following configuration:

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
sudo a2ensite fmu.edu.pk.conf
sudo a2dissite 000-default.conf
sudo systemctl restart apache2
```

### Step 8: Configure Google Cloud Firewall

In Google Cloud Console, ensure your firewall allows HTTP and HTTPS:

1. Go to **VPC Network** → **Firewall**
2. Create rules to allow:
   - **HTTP**: TCP port 80
   - **HTTPS**: TCP port 443
3. Apply rules to your VM instance

### Step 9: Point Your Domain to Google Cloud

In your domain registrar (where fmu.edu.pk is registered), add DNS records:

| Type | Name | Value |
|------|------|-------|
| A | @ | YOUR_GOOGLE_CLOUD_EXTERNAL_IP |
| A | www | YOUR_GOOGLE_CLOUD_EXTERNAL_IP |

**Note**: DNS changes may take up to 48 hours to propagate.

### Step 10: Run Drupal Installer

1. Open your browser and go to: `http://fmu.edu.pk/`
2. Drupal will automatically redirect to the installation wizard
3. Select **Standard** installation profile
4. Enter your database credentials from Step 5
5. Set your site name as **Faisalabad Medical University**
6. Create your admin account (save these credentials securely!)

### Step 11: Secure Your Installation (Important!)

After installation, secure the settings file:

```bash
chmod 444 web/sites/default/settings.php
```

### Step 12: Enable HTTPS with Let's Encrypt

For a production website, enable HTTPS:

```bash
sudo apt install certbot python3-certbot-apache
sudo certbot --apache -d fmu.edu.pk -d www.fmu.edu.pk
```

This will automatically configure SSL certificates and redirect HTTP to HTTPS.

---

## 📝 After Installation: Creating Content

Once Drupal is installed, you'll need to create the content structure. Here's what to do:

### 1. Install Essential Modules

Log into your Drupal admin (`/admin`) and install these modules:

```bash
cd /var/www/FMUwebsite
composer require drupal/admin_toolbar drupal/pathauto drupal/metatag drupal/ctools drupal/token drupal/webform
```

Then enable them in Drupal admin or via Drush.

### 2. Create Content Types

In Drupal admin, go to **Structure** → **Content types** and create:

- **Department** – For each university department
- **Faculty Member** – For staff profiles
- **News Article** – For announcements
- **Event** – For upcoming events
- **Page** – For static pages (About, Contact, etc.)

### 3. Create Menus

Go to **Structure** → **Menus** and set up:

- Main navigation (Academics, Admissions, Research, etc.)
- Footer menu (Contact, Privacy Policy, etc.)

### 4. Import Content from pmc.edu.pk

Content from pmc.edu.pk can be:
- Manually copied and formatted
- Migrated using Drupal's Migration modules (if you have database access to the old site)

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
