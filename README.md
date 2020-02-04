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
^^^ Change the following line; ^^^
$USER ALL=(ALL) PASWD: ALL

##### Secure SSH Configuration
```bash
sudo nano /etc/ssh/sshd_config 
```
^^^ Change the following lines ^^^
AllowUsers $USER
Port 65000


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

##### Fail2Ban



