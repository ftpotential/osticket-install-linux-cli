
Implementing a Help Desk Ticketing System (osTicket) in Ubuntu Server using Azure Virtual Machines
# osticket-install-linux-cli

## Objective

In this project, we will automate the installation of OsTicket on an Ubuntu server using a shell script. The script will update packages, install Apache, PHP, and MySQL, download and configure OsTicket, and set up the necessary file permissions. By the end, we’ll have a fully operational OsTicket system, accessible via a web browser, similar to those used in IT departments for support operations.

### Skills Learned

- Shell Scripting: Automating system tasks and software installations.
- Linux Server Management: Configuring and managing Ubuntu servers.
- Web Server Setup: Installing and configuring Apache and PHP.
- Database Configuration: Setting up MySQL databases and users securely.
- File Permissions: Managing file permissions and security in Linux.
- OsTicket Installation: Deploying and configuring a ticketing system.

### Tools Used

- Ubuntu Server
- Bash (Shell Scripting)
- Apache Web Server
- PHP
- MySQL
- OsTicket
- wget (for downloading files)
- unzip (for extracting compressed files)

## Steps

Spin Up an Ubuntu Instance
Deploy an Ubuntu server instance using Microsoft Azure.

Create the Installation Script and Make it Executable:
```bash
touch osTicket.sh
chmod +x osTicket.sh
```
open script:
```
nano osTicket.sh
```
Paste this into the script and change database name, user and password for security purposes:
```bash
#!/usr/bin/env bash

sudo apt update -y
# Update the package list to ensure the latest versions are installed

sudo apt install unzip apache2 php7.2 php7.2-mysql mysql-server -y
# Install unzip, Apache web server, PHP 7.2, MySQL server, and the PHP MySQL extension

sudo a2enmod rewrite
# Enable the Apache rewrite module, which is necessary for OsTicket

sudo rm /var/www/html/index.html
# Remove the default Apache index.html file

mkdir osticket && cd osticket
# Create a directory for OsTicket and cd into it

wget https://github.com/osTicket/osTicket/releases/download/v1.14.1/osTicket-v1.14.1.zip
# Download the OsTicket installation package

unzip osTicket-v1.14.1.zip
# Unzip the downloaded OsTicket package

sudo mv upload/* /var/www/html
# Move the OsTicket files to the Apache web root directory

sudo mv /var/www/html/include/ost-sampleconfig.php /var/www/html/include/ost-config.php
# Rename the sample configuration file for OsTicket

sudo chmod 0666 /var/www/html/include/ost-config.php
# Set the necessary permissions for the OsTicket configuration file

sudo mysql -u root -e "CREATE DATABASE osticket-x98h5s7x3;"
# Create a new MySQL database for OsTicket

sudo mysql -u root -e "CREATE USER 'osticket-wh9d773u5'@'localhost' IDENTIFIED BY '7x%2J#9p6@';"
# Create a new MySQL user with a secure password

sudo mysql -u root -e "GRANT ALL PRIVILEGES ON * . * TO 'osticket-wh9d773u5'@'localhost';"
# Grant all privileges to the newly created MySQL user for the OsTicket database

sudo chown ubuntu:ubuntu /etc/apache2/sites-available/000-default.conf
# Change the ownership of the Apache configuration file to the current user

cat <<EOF >> /etc/apache2/sites-available/000-default.conf
<Directory /var/www/>
AllowOverride All
</Directory>
EOF
# Add a configuration block to allow .htaccess overrides in the Apache web directory

sudo service apache2 restart
# Restart the Apache service to apply all changes

```

⚠️ Security Warning: No SSL/TLS Encryption


This guide is intended for educational purposes and demonstrates how to set up the OsTicket system on an Ubuntu server using Azure Virtual Machines. This setup does not include SSL/TLS certificates, which means the web interface is only accessible over HTTP. As a result, it is insecure and exposed to the public internet, making it vulnerable to interception and attacks.

Allow HTTP and HTTPS Traffic
On your Ubuntu server, you need to allow HTTP and HTTPS traffic. paste and run:
```
sudo ufw allow 'Apache Full'
sudo ufw enable
```

Go to the Azure portal, navigate to the "Networking" section of your virtual machine, and add inbound security rules to allow traffic on ports 80 (HTTP) and 443 (HTTPS)

Access the OsTicket Setup Page
Open a browser and navigate to http://VM-IPADDRESS/setup and follow the wizard.

The script has created a database:
```
Database Name: osticket-x98h5s7x3
Database User: osticket-wh9d773u5
Database Password: 7x%2J#9p6@
```
Post-Installation: Clean Up

After the installation, delete the setup directory and reset file permissions:
```
sudo rm -rf /var/www/html/setup
sudo chmod 0644 /var/www/html/include/ost-config.php
```

