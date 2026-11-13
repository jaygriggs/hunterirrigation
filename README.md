
Drupal Update Hook textbook Definition

A Drupal update hook (hook_update_N()) is a numbered PHP function placed inside a module's .install file to make programmatic changes to the database, entities, or configuration when deploying updates. Drupal runs each update hook only once because it tracks the highest executed update number for every module in the database and only triggers functions with a number greater than that stored value.

How hook_update_N() Works

• Location & Naming: Update functions live in my_module.install and follow the strict naming convention my_module_update_N(), where N is a sequential integer (for example, 10001 for Drupal 10, update 01).
• Execution: When you run drush updb (or visit /update.php), Drupal scans all enabled modules for hook_update_N() functions.
• Batch Support: Each function receives a &$sandbox array parameter, allowing long-running database updates to process data in small batches without hitting PHP memory or timeout limits.

Why Drupal Runs It Only Once

• Schema Version Tracking: Drupal stores each module's current schema version in the key_value database table (under the system.schema collection).
• Sequential Comparison: When drush updb runs, Drupal compares the function numbers in your .install file against the number saved in the database.
• Automatic Increment: Once my_module_update_10001() finishes without errors, Drupal updates the module's stored schema version to 10001. On future deployments, Drupal skips 10001 and only executes newly added functions with higher numbers (such as 10002).





Setup steps

1. Project & DDEV Setup

# 1. Create the Drupal 10 project inside DDEV
ddev composer create "drupal/recommended-project:^10"

# 2. Install Drush
ddev composer require drush/drush

# 3. Reset settings and verify DDEV config
rm -f web/sites/default/settings.php web/sites/default/settings.ddev.php
ddev config --project-type=drupal10 --docroot=web
ddev restart

# 4. Install the Drupal site with the Umami profile
ddev drush site:install demo_umami --account-name=admin --account-pass="admin123" -y

# 5. Open the site in your browser
ddev launch

2. Git & GitHub Connection


# 1. Create a Drupal + DDEV .gitignore
cat << 'EOF' > .gitignore
# Composer & Drupal core
/vendor/
/web/core/
/web/modules/contrib/
/web/themes/contrib/
/web/profiles/contrib/
/web/libraries/

# Drupal generated files & local settings
/web/sites/*/settings*.php
!/web/sites/default/default.settings.php
/web/sites/*/files/
/web/sites/*/private/

# DDEV local/generated files (keeps .ddev/config.yaml tracked)
/.ddev/*
!/.ddev/config.yaml

# macOS & IDE files
.DS_Store
.idea/
.vscode/
EOF

# 2. Initialize Git and make the initial commit
git init -b main
git add .
git commit -m "Initial Drupal 10 Umami setup with DDEV"

# 3. Connect to GitHub and push
git remote add origin https://github.com
git push -u origin main


Tasks

Main Task #1

# 1. Needed to Login so
ddev drush uli

# 2. Create Person with Editor role
https://hunterirrigation.ddev.site/en/admin/people/create
Username jaygriggseditor

# 3. Go here to change site name
https://hun

# 4. verify with ddev drush config:get system.site

Expected result

name
_core:
default_config_hash: ijfbzDTN4CbE7Sr-6ubWzy_t1vH4OtU1doNCLssVz-4
langcode: en
uuid: 4e9dd062-cbfe-45c7-a318-b92715d97bbb
name: "Jay's demo site"
mail: admin@example.com
slogan: ''
page:
403: ''
404: ''
front: /node
admin_compact_mode: false
weight_select_max: 100
default_langcode: en
mail_notification: null

Found in system.site.yml

Config Sync Setup

# 1. Followed these steps for my usual config sync setup

mkdir -p config/sync
rm -rf web/sites/default/files/sync

chmod u+w web/sites/default/settings.php
echo "\$settings['config_sync_directory'] = '../config/sync';" >> web/sites/default/settings.php
chmod u-w web/sites/default/settings.php

ddev drush cr
ddev drush cex -y
git status

Main Task #2

# 1. Using composer install modules

ddev composer require drupal/admin_toolbar drupal/pathauto

# 2. enable them

ddev drush en admin_toolbar pathauto

Main Task #3

# 1. Create the custom module

# 2. run clear cache and update db commands
ddev drush cr
ddev drush updb -y

## I would ask about Spanish and hola cazador append to Spanish nodes but not noted here so did not do.

# 3. verify
ddev drush sqlq "SELECT nid, title FROM node_field_data WHERE type = 'recipe' AND title LIKE '%Hello Hunter%';"

Main Task #4

# 1. Create the view via the UI

# 2. export config for the codebase
ddev drush cex

Main Task #5

# 1. Install Barrio

ddev composer require drupal/bootstrap_barrio

# 2. Create Sub Theme

# 3. Enable new sub theme, set as default and clear cache

ddev drush theme:enable hunter_barrio -y
ddev drush config:set system.theme default hunter_barrio -y
ddev drush cr

# 4. Style theme
table striped and table responsive get called by preprocessor hook
Added one rule

Main Task #6

# 1. Drush command
ddev drush status
ddev drush cr
ddev drush cex -y

ddev drush status
Drupal version   : 10.6.21
Site URI         : https://hunterirrigation.ddev.site
DB driver        : mysql
DB hostname      : db
DB port          : 3306
DB username      : db
DB name          : db
Database         : Connected
Drupal bootstrap : Successful
Default theme    : hunter_barrio
Admin theme      : claro
PHP binary       : /usr/bin/php8.2
PHP config       : /etc/php/8.2/cli/php.ini
PHP OS           : Linux
PHP version      : 8.2.20
Drush script     : /var/www/html/vendor/bin/drush.php
Drush version    : 13.6.2.0
Drush temp       : /tmp
Drush configs    : /var/www/html/vendor/drush/drush/drush.yml
Install profile  : demo_umami
Drupal root      : /var/www/html/web
Site path        : sites/default
Files, Public    : sites/default/files
Files, Temp      : /tmp
Drupal config    : ../config/sync         
