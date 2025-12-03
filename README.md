# FMU Website – Drupal Project

This is the official website codebase for **Faisalabad Medical University (FMU)**, built on [Drupal](https://www.drupal.org/). This repository uses the `drupal/recommended-project` Composer template to manage Drupal core, contributed modules, and dependencies.

## Prerequisites

- **PHP**: 8.1 or higher (8.3+ recommended)
- **Composer**: 2.x
- **Database**: MySQL 8.0+ / MariaDB 10.6+ / PostgreSQL 13+
- **Web Server**: Apache 2.4+ with mod_rewrite, or Nginx 1.x

## Quick Start

### 1. Clone the Repository

```bash
git clone https://github.com/munaimtahir/FMUwebsite.git
cd FMUwebsite
```

### 2. Install Dependencies

```bash
composer install
```

This will download Drupal core, contributed modules, and all PHP dependencies into the `vendor/` directory.

### 3. Configure Your Web Server

Point your web server's document root to the `web/` directory:

```
/path/to/FMUwebsite/web
```

Ensure that:
- URL rewriting (mod_rewrite for Apache) is enabled
- PHP is properly configured and accessible

### 4. Install Drupal

Navigate to your site in a browser. If this is a fresh installation, Drupal's installer will guide you through:
- Database configuration
- Site name and admin account setup
- Initial configuration

Alternatively, access the installer directly at:
```
http://your-local-domain/core/install.php
```

## Configuration Management

This project is set up for Drupal's configuration export/import system:

- **Config sync directory**: `config/sync/`
- To enable, add the following to your `settings.php`:
  ```php
  $settings['config_sync_directory'] = '../config/sync';
  ```

Configuration files will be stored in `config/sync/` and can be version controlled. This allows for:
- Consistent deployments across environments
- Code review of configuration changes
- Rollback capabilities

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

## Development Workflow

1. Make changes to custom modules/themes in `web/modules/custom/` and `web/themes/custom/`
2. Use `drush` or the Drupal UI to export configuration changes to `config/sync/`
3. Commit your code and configuration changes
4. On deployment, run `composer install` and import configuration

## License

This project is for Faisalabad Medical University. See LICENSE file for details (if applicable).
