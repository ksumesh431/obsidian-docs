Here’s the complete and updated step-by-step guide for installing Observium on **Amazon Linux 2023**, including all the modifications we made during troubleshooting. This guide is tailored to include the fixes for **MariaDB**, **cron jobs**, and the missing **RRD** and **logs** directories.

---

## **Step 1: Update the System**
Start by updating your system to ensure all packages are up to date.

```bash
sudo yum update -y
```

---

## **Step 2: Install Required Dependencies**
Install the necessary dependencies for Observium, including Apache, PHP, MariaDB, and other tools.

```bash
sudo yum install -y httpd mariadb105-server mariadb105 php php-cli php-mysqlnd \
php-gd php-snmp php-mbstring php-xml php-posix net-snmp net-snmp-utils \
rrdtool unzip wget git cronie
```

---

## **Step 3: Start and Enable Services**
Start and enable the required services (Apache, MariaDB, and SNMP).

```bash
sudo systemctl start httpd
sudo systemctl enable httpd

sudo systemctl start mariadb
sudo systemctl enable mariadb

sudo systemctl start snmpd
sudo systemctl enable snmpd

sudo systemctl start crond
sudo systemctl enable crond
```

---

## **Step 4: Secure MariaDB**
Run the `mysql_secure_installation` script to secure your MariaDB installation.

```bash
sudo mysql_secure_installation
```

Follow the prompts to:
- Set a root password.
- Remove anonymous users.
- Disallow root login remotely.
- Remove the test database.
- Reload privilege tables.

---

## **Step 5: Create a Database for Observium**
Log in to MariaDB and create a database and user for Observium.

```bash
sudo mysql -u root -p
```

Run the following SQL commands (replace `your_password` with a strong password):

```sql
CREATE DATABASE observium;
CREATE USER 'observium'@'localhost' IDENTIFIED BY 'your_password';
GRANT ALL PRIVILEGES ON observium.* TO 'observium'@'localhost';
FLUSH PRIVILEGES;
EXIT;
```

---

## **Step 6: Download and Install Observium**
1. Navigate to the `/opt` directory:

   ```bash
   cd /opt
   ```

2. Download the Observium Community Edition:

   ```bash
   sudo wget http://www.observium.org/observium-community-latest.tar.gz
   ```

3. Extract the downloaded file:

   ```bash
   sudo tar zxvf observium-community-latest.tar.gz
   ```

4. Check the extracted directory structure:

   ```bash
   ls -l /opt
   ```

   If the extracted directory is already named `observium`, proceed to the next step. If it has a different name (e.g., `observium-community`), rename it:

   ```bash
   sudo mv observium-community observium
   ```

---

## **Step 7: Configure Observium**
1. Copy the default configuration file:

   ```bash
   sudo cp /opt/observium/config.php.default /opt/observium/config.php
   ```

2. Edit the `config.php` file to set your database credentials:

   ```bash
   sudo nano /opt/observium/config.php
   ```

   Update the following lines with your database details:

   ```php
   $config['db_host'] = 'localhost';
   $config['db_user'] = 'observium';
   $config['db_pass'] = 'your_password';
   $config['db_name'] = 'observium';
   ```

---

## **Step 8: Initialize the Database**
Run the following command to initialize the Observium database:

```bash
sudo php /opt/observium/discovery.php -u
```

---

## **Step 9: Set Up the Web Interface**
1. Create an Apache virtual host configuration for Observium:

   ```bash
   sudo nano /etc/httpd/conf.d/observium.conf
   ```

   Add the following content:

   ```apache
   <VirtualHost *:80>
       DocumentRoot /opt/observium/html/
       ServerName your_server_domain_or_IP

       <Directory "/opt/observium/html/">
           AllowOverride All
           Require all granted
       </Directory>
   </VirtualHost>
   ```

2. Restart Apache to apply the changes:

   ```bash
   sudo systemctl restart httpd
   ```

---

## **Step 10: Add an Admin User**
Create an admin user for Observium:

```bash
sudo php /opt/observium/adduser.php admin your_password 10
```

---

## **Step 11: Set Up Cron Jobs**
Observium requires cron jobs for polling and discovery. Since Amazon Linux 2023 may not have the `/etc/cron.d` directory by default, follow these steps:

Ensure that the cron service (`crond`) is installed and running on your system.

1. Install the cron service if it’s not already installed:

   ```bash
   sudo yum install -y cronie
   ```

2. Start and enable the cron service:

   ```bash
   sudo systemctl start crond
   sudo systemctl enable crond
   ```

3. Verify that the cron service is running:

   ```bash
   sudo systemctl status crond
   ```


4. Create the `/etc/cron.d` directory if it doesn’t exist:

   ```bash
   sudo mkdir -p /etc/cron.d
   sudo chmod 755 /etc/cron.d
   ```

5. Create the Observium cron job file:

   ```bash
   sudo nano /etc/cron.d/observium
   ```

6. Add the following lines to the file:

   ```cron
   33  */6   * * *   root    /opt/observium/discovery.php -h all >> /dev/null 2>&1
   */5 *     * * *   root    /opt/observium/poller-wrapper.py 1 >> /dev/null 2>&1
   01 0 *     * *     root    /opt/observium/housekeeping.php -ysel >> /dev/null 2>&1
   ```

7. Save and exit the file, then restart the cron service:

   ```bash
   sudo systemctl restart crond
   ```

---

## **Step 12: Fix Missing RRD and Logs Directories**
If you see warnings about missing directories for RRD and logs, create them and set the correct permissions:

8. Create the directories:

   ```bash
   sudo mkdir -p /opt/observium/rrd
   sudo mkdir -p /opt/observium/logs
   ```

9. Set the correct ownership and permissions:

   ```bash
   sudo chown -R apache:apache /opt/observium/rrd
   sudo chown -R apache:apache /opt/observium/logs
   sudo chmod -R 775 /opt/observium/rrd
   sudo chmod -R 775 /opt/observium/logs
   ```

10. Restart Apache:

   ```bash
   sudo systemctl restart httpd
   ```

---

## **Step 13: (Optional)Adjust Firewall Rules**
If you have a firewall enabled, allow HTTP traffic:

```bash
sudo firewall-cmd --permanent --add-service=http
sudo firewall-cmd --reload
```

---

## **Step 14: Access Observium**
Open your web browser and navigate to your server's IP or domain name:

```
http://your_server_domain_or_IP
```

Log in using the admin credentials you created earlier.

---

### **Optional: Enable HTTPS**
For better security, consider setting up an SSL certificate using Let's Encrypt or another provider. You can use Certbot to automate the process.

---



---
---
---

# Alternative bash script
``` bash
#!/bin/bash

# Observium Installation Script for Amazon Linux 2023
# Description: Automates the installation and configuration of Observium

# Variables
DB_ROOT_PASSWORD="prologic"
DB_NAME="observium"
DB_USER="proot"
DB_PASSWORD="prologic"
ADMIN_USER="admin"
ADMIN_PASSWORD="prologic"

# Step 1: Update the System
echo "Updating the system..."
sudo yum update -y

# Step 2: Install Required Dependencies
echo "Installing required dependencies..."
sudo yum install -y httpd mariadb105-server mariadb105 php php-cli php-mysqlnd \
php-gd php-snmp php-mbstring php-xml php-posix net-snmp net-snmp-utils \
rrdtool unzip wget git cronie

# Step 3: Start and Enable Services
echo "Starting and enabling services..."
sudo systemctl start httpd
sudo systemctl enable httpd

sudo systemctl start mariadb
sudo systemctl enable mariadb

sudo systemctl start snmpd
sudo systemctl enable snmpd

sudo systemctl start crond
sudo systemctl enable crond

# Step 4: Secure MariaDB
echo "Securing MariaDB..."
sudo mysql_secure_installation <<EOF

y
$DB_ROOT_PASSWORD
$DB_ROOT_PASSWORD
y
y
y
y
EOF

# Step 5: Create a Database for Observium
echo "Creating database and user for Observium..."
sudo mysql -u root -p"$DB_ROOT_PASSWORD" <<EOF
CREATE DATABASE $DB_NAME;
CREATE USER '$DB_USER'@'localhost' IDENTIFIED BY '$DB_PASSWORD';
GRANT ALL PRIVILEGES ON $DB_NAME.* TO '$DB_USER'@'localhost';
FLUSH PRIVILEGES;
EXIT;
EOF

# Step 6: Download and Install Observium
echo "Downloading and installing Observium..."
cd /opt
sudo wget http://www.observium.org/observium-community-latest.tar.gz
sudo tar zxvf observium-community-latest.tar.gz
sudo mv observium-community observium 2>/dev/null || true

# Step 7: Configure Observium
echo "Configuring Observium..."
sudo cp /opt/observium/config.php.default /opt/observium/config.php

# Use variables to dynamically replace placeholders in config.php
sudo sed -i "s/'USERNAME'/'$DB_USER'/" /opt/observium/config.php
sudo sed -i "s/'PASSWORD'/'$DB_PASSWORD'/" /opt/observium/config.php

# Step 8: Initialize the Database
echo "Initializing the Observium database..."
sudo php /opt/observium/discovery.php -u

# Step 9: Set Up the Web Interface
echo "Setting up the web interface..."
sudo bash -c 'cat > /etc/httpd/conf.d/observium.conf' <<EOF
<VirtualHost *:80>
    DocumentRoot /opt/observium/html/
    ServerName your_server_domain_or_IP

    <Directory "/opt/observium/html/">
        AllowOverride All
        Require all granted
    </Directory>
</VirtualHost>
EOF
sudo systemctl restart httpd

# Step 10: Add an Admin User
echo "Creating an admin user for Observium..."
sudo php /opt/observium/adduser.php $ADMIN_USER $ADMIN_PASSWORD 10

# Step 11: Set Up Cron Jobs
echo "Setting up cron jobs for Observium..."
sudo mkdir -p /etc/cron.d
sudo chmod 755 /etc/cron.d
sudo bash -c 'cat > /etc/cron.d/observium' <<EOF
33  */6   * * *   root    /opt/observium/discovery.php -h all >> /dev/null 2>&1
*/5 *     * * *   root    /opt/observium/poller-wrapper.py 1 >> /dev/null 2>&1
01 0 *     * *     root    /opt/observium/housekeeping.php -ysel >> /dev/null 2>&1
EOF
sudo systemctl restart crond

# Step 12: Fix Missing RRD and Logs Directories
echo "Fixing missing RRD and logs directories..."
sudo mkdir -p /opt/observium/rrd
sudo mkdir -p /opt/observium/logs
sudo chown -R apache:apache /opt/observium/rrd
sudo chown -R apache:apache /opt/observium/logs
sudo chmod -R 775 /opt/observium/rrd
sudo chmod -R 775 /opt/observium/logs
sudo systemctl restart httpd

echo "Observium installation and configuration completed successfully!"


```