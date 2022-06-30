# How-to-Install-and-Configure-Nextcloud-Hub-21
Nextcloud is a suite of client-server software for creating and using file hosting services. It is enterprise-ready with comprehensive support options. Being free and open-source software, anyone is allowed to install and operate it on their own private server devices

## Install Apache & MySQL
    sudo apt update
    sudo apt install apache2
    sudo apt install mysql-server
## Secure MySQL Database
  sudo mysql_secure_installation
## VALIDATE PASSWORD COMPONENT: n
## New root passwort: <YOUR MYSQL PASSWORD>
## Re-enter new password: <YOUR MYSQL PASSWORD>
## Remove anonymous user: y
## Disallow root login remotely: y
## Remove test database and access to it: y
## Reload privileges tables now: y
## Prepare MySQL database
    sudo mysql -u root -p
## <ENTER YOUR MYSQL PASSWORD>
## mysql> create database nextcloud;
## mysql> create user 'nextcloud'@'localhost' identified by 'Passw0rd';
## mysql> grant all privileges on nextcloud.* to 'nextcloud'@'localhost';
## mysql> flush privileges;
## mysql> quit

Install PHP for Nextcloud
    sudo apt install php7.4
    sudo apt install php7.4-gd php7.4-mysql php7.4-curl php7.4-mbstring
    sudo apt install php7.4-intl php7.4-gmp php7.4-bcmath php7.4-xml
    sudo apt install libapache2-mod-php7.4 php7.4-zip php-imagick php-apcu
## Download Nextcloud Hub 21
    wget https://download.nextcloud.com/server/releases/nextcloud-21.0.1.zip
## Next, unzip Nextcloud directly into the www directory of Apache (first run sudo apt install unzip if you don’t have unzip installed yet):

    sudo unzip nextcloud-21.0.1.zip -d /var/www

## Switch directories and change ownership of the Nextcloud folder to Apache, which is the “www-data” user:
    cd /var/www
    sudo chown -R www-data:www-data nextcloud/

## Configure Apache for Nextcloud
    sudo a2enmod headers env dir mime rewrite
    sudo service apache2 restart
## 
    /etc/apache2/sites-available/000-default.conf
##  Copy and pest
  
    <VirtualHost *:80>

    ServerName cloud.yourdomain.com
    DocumentRoot /var/www/nextcloud

    <Directory /var/www/nextcloud/>
        Require all granted
        AllowOverride All
        Options FollowSymLinks MultiViews

        <IfModule mod_dav.c>
            Dav off
        </IfModule>

        RewriteEngine On
        RewriteRule ^/\.well-known/carddav https://%{SERVER_NAME}/remote.php/dav/ [R=301,L]
        RewriteRule ^/\.well-known/caldav https://%{SERVER_NAME}/remote.php/dav/ [R=301,L]
        RewriteRule ^/\.well-known/host-meta https://%{SERVER_NAME}/public.php?service=host-meta [QSA,L]
        RewriteRule ^/\.well-known/host-meta\.json https://%{SERVER_NAME}/public.php?service=host-meta-json [QSA,L]
        RewriteRule ^/\.well-known/webfinger https://%{SERVER_NAME}/public.php?service=webfinger [QSA,L]

    </Directory>

    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined

</VirtualHost>

## Finally, restart Apache
    sudo service apache2 restart
## Fixing Installation Issues
## Update PHP Configuration
    Cd /etc/php/7.4/apache2
## Then edit php.ini file
## memory_limit = 512M

    /var/www/nextcloud/config/config.php
  
    'trusted_domains' => 
    array(
      0 => 192.168.1.50:80
      1 => http://yourdomain.com
    ),
## Default Phone region
## 'default_phone_region' => 'GB',
## memory cache configuration
## 'memcache.local' => '\OC\Memcache\APCu',

## If Nextcloud is not installed in a subfolder, update the .htaccess file
    sudo -u www-data php /var/www/nextcloud/occ maintenance:update:htaccess

## Set up Cronjob for Nextcloud
## sud
## If asked, press “1” to use the nano editor (which is super easy to use) and add the following line to your crontab file:
## */5  *  *  *  * php -f /var/www/nextcloud/cron.php
## Fix php-imagick warning
    sudo apt remove imagemagick-6-common php-imagick
    sudo apt autoremove
    and then reinstall imagemagick;
    sudo apt install imagemagick php-imagick

