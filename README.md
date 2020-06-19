# RaspberryPi_FileServer

## Requirements List
<ol>
  <li>Raspberry Pi B+</li>
  <li> Micro SD Card </li>
  <li> Gigabit Ethernet Cable (CAT 5e) </li>
  <li> USB Hub </li>
  <li> Four Hard Drives </li>
</ol>

### Intial Setup

Change the root password!
```bash
sudo passwd
```

Install SSH
```bash
sudo apt -y install openssh-server
```

### Improve Security
Create a new user, get rid of old pi user.
```bash
sudo adduser collin
```

Give group permissions to your new user, replace $USER with your username.
```bash
sudo usermod -aG adm,dialout,cdrom,sudo,audio,video,plugdev,games,users,input,netdev,gpio,i2c,spi $USER
```

Kill and delete pi user
```bash
sudo pkill -u pi

sudo deluser pi

sudo deluser -remove-home pi
```

Make sure sudo requires a password at all times
```bash
sudo nano /etc/sudoers.d/010_pi-nopassword
```
**^^^ Change the following line; ^^^**
```plain
$USER ALL=(ALL) PASWD: ALL
```

##### Secure SSH Configuration
```bash
sudo nano /etc/ssh/sshd_config 
```
**^^^ Change the following lines ^^^**
```plain
AllowUsers $USER
Port 65000
```

Reload you're SSH server
```bash
sudo systemctl reload sshd
```

##### Firewall Configuration
Install the UFW (Uncomplicated Firewall) firwall.
```bash
sudo apt -y install ufw

sudo ufw enable

# allow traffic to your ssh port
sudo ufw allow 65000

# deny any attempts at port 22
sudo ufw deny 22

#allow http and https traffic, if using a webserver
sudo ufw allow http
# or 
sudo ufw allow 80

sudo ufw allow https
# or
sudo ufw allow 443

#check ufw's settings
sudo ufw status

# limit ssh login attempts
sudo ufw limit ssh/tcp
```
##### Docker

Download and run the docker convience script
```bash
curl -fsSL https://get.docker.com -o get-docker.sh
sudo sh get-docker.sh
```
Add user to docker group 
```bash
sudo usermod -aG docker $USER
```

Verify Installation 
```bash
docker version
docker run hello-world
```

## NextCloud Personal Cloud Storage

##### Install Apache2
```bash
sudo apt -y install apache2
```

##### Install PHP 7.3 and additional packages
```
sudo apt -y install php7.3 php7.3-gd php7.3-sqlite3 php7.3-curl php7.3-zip php7.3-xml php7.3-mbstring php7.3-mysql php7.3-bz2 php7.3-intl php7.3-smbclient php7.3-imap php7.3-gmp
```

##### Install MariaDB Server and Client
```bash
sudo apt -y install mariadb-client mariadb-server
```

##### Restart Apache
```bash
sudo systemctl restart apache2
```

##### Log into MYSQL server
```bash
sudo mysql -u root -p
```

##### Create the NextCloud Database
```sql
CREATE DATABASE nextclouddb;
```

##### Create user fill in PASSWORD with your desired password
```sql
CREATE USER 'nextclouduser'@'localhost' IDENTIFIED BY PASSWORD;
```

##### Grant Privlages to nextcloud user and flush privlages
```sql
GRANT ALL PRIVILEGES ON nextclouddb.* TO 'nextclouduser'@'localhost';
FLUSH PRIVILEGES;
```

### Installing NextCloud
```bash
cd /var/www
```
##### Download Latest version of nextcloud
```bash
curl https://download.nextcloud.com/server/releases/latest.tar.bz2 | sudo tar -jxv
```

##### Make Data Directory
```bash
sudo mkdir -p /var/www/nextcloud/data
```

##### User Controls
```bash
sudo chown -R www-data:www-data /var/www/nextcloud/
sudo mkdir -p /var/www/nextcloud/data
```

### Apache Configuration 
```bash
sudo vim /etc/apache2/sites-available/nextcloud.conf
```

```conf
Alias /nextcloud "/var/www/nextcloud/"

<Directory /var/www/nextcloud/>
  Require all granted
  AllowOverride All
  Options FollowSymLinks MultiViews

  <IfModule mod_dav.c>
    Dav off
  </IfModule>

</Directory>
```

##### Tell Apache to use the configuration file
```bash
sudo a2ensite nextcloud.conf
```

```bash
sudo systemctl reload apache2
```

GO TO `IPADDRESS/nextcloud` and continue setup

