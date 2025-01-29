# Mandatory part

## Switch to root user
``` bash
su -
```

## Update the systeme
``` bash
apt update 
apt upgrade 
```

## Install sudo and configure strict rules
``` bash
apt install sudo
sudo visudo
    Defaults    passwd_tries=3
    Defaults    badpass_message="Try again..."
    Defaults    logfile="/var/log/sudo/sudo.log"
    Defaults    log_input,log_output
    Defaults    requiretty
    Defaults    secure_path="/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/snap/bin"
```

## Manage users
``` bash
sudo usermod -aG sudo login
getent group sudo
sudo addgroup user42
sudo usermod -aG user42 login
getent group user42
sudo visudo
    login    ALL=(ALL) ALL
sudo adduser new_username new_group
```

## SSH
``` bash
sudo apt update 
sudo apt upgrade 
sudo apt install openssh-server
service ssh restart
sudo nano /etc/ssh/sshd_config
    Port 4242
    PermiRootLogin no
service ssh restart
```

## UFW
``` bash
sudo apt install ufw
sudo ufw enable
sudo ufw allow 4242
sudo ufw status
```

## AppArmor Status
``` bash
sudo aa-status
```

## Setup strong password policy
``` bash
sudo nano /etc/login.defs
    PASS_MAX_DAYS   30
    PASS_MIN_DAYS   2
    PASS_WARN_AGE   7

sudo apt install libpam-pwquality
sudo nano /etc/pam.d/common-password
    password    requisite         pam_pwquality.so minlen=10 lcredit =-1 ucredit=-1 dcredit=-1
    maxrepeat=3 usercheck=1 difok=7 enforce_for_root

# [ Same as bellow but enforce rule to apply ]
sudo nano /etc/security/pwquality.conf
    minlen = 10           # Minimum password length.
    ucredit = -1          # At least 1 uppercase letter required.
    lcredit = -1          # At least 1 lowercase letter required.
    dcredit = -1          # At least 1 digit required.
    maxrepeat = 3         # Maximum consecutive identical characters allowed.
    usercheck=0           # Not include the name of the user.
    difok = 7             # Requires 7 characters different from the old password.
    enforce_for_root      # Enforces the policy for root passwords.
sudo reboot
```

## Change Password
``` bash
chage -l login             # For users
sudo passwd root           # For root
```

## Change Hostname
``` bash
hostnamectl set-hostname new_hostname
sudo nano /etc/hosts
    127.0.0.1       localhost
    127.0.0.1       new_hostname
sudo reboot
```

## Create monitoring.sh
``` bash
• The architecture of your operating system and its kernel version:
    uname -a
• The number of physical processors:
    grep "physical id" /proc/cpuinfo | sort | uniq | wc -l
• The number of virtual processors:
    grep "processor" /proc/cpuinfo | wc -l
• The current available RAM on your server and its utilization rate as a percentage:
    usage RAM: free -m | awk '/Mem:/ {printf $3}' --> $memory_used
    total RAM: free -m | awk '/Mem:/ {printf $2}' -->  $memory_total
    porcentage RAM: awk "BEGIN {printf \"%.2f\", $memory_used/$memory_total*100}"
• The current available memory on your server and its utilization rate as a percentage.
    usage Disk: df -h / | awk '/\// {print $3}' --> $disk_used
    total Disk: df -h / | awk '/\// {print $2}' --> $disk_total
    percentage Disk: df -h / | awk '/\// {print $5}'
• The current utilization rate of your processors as a percentage:
    top -bn1 | awk '/Cpu\(s\)/ {printf "%.1f%%", $2 + $4}'
• The date and time of the last reboot:
    who -b | awk '{print $3 " " $4}'
• Whether LVM is active or not:
    [ $(lsblk | grep "lvm" | wc -l) -gt 0 ] && echo "yes" || echo "no"
• The number of active connections:
    ss -Ht state established | wc -l
• The number of users using the server:
    users | wc -w
• The IPv4 address of your server and its MAC (Media Access Control) address:
    hostname -I | awk '{print $1}'     --> ip_address
    ip link | awk '/ether/ {print $2}' --> mac_address
• The number of commands executed with the sudo program:
    journalctl -q _COMM=sudo | grep COMMAND | wc -l
```

## `Full Script`
``` bash
nano /path/monitoring.sh
```
``` sh
#!/bin/bash

# Architecture and kernel version
architecture=$(uname -a)

# Number of physical and virtual processors
physical_processors=$(grep "physical id" /proc/cpuinfo | sort -u | wc -l)
virtual_processors=$(grep -c "processor" /proc/cpuinfo)

# RAM usage
memory_used=$(free -m | awk '/Mem:/ {print $3}')
memory_total=$(free -m | awk '/Mem:/ {print $2}')
memory_percentage=$(awk "BEGIN {printf \"%.2f\", $memory_used/$memory_total*100}")

# Disk usage
disk_used=$(df -h / | awk '/\// {print $3}')
disk_total=$(df -h / | awk '/\// {print $2}')
disk_percentage=$(df -h / | awk '/\// {print $5}')

# CPU load
cpu_load=$(top -bn1 | awk '/Cpu\(s\)/ {printf "%.1f%%", $2 + $4}')

# Last reboot
last_reboot=$(who -b | awk '{print $3 " " $4}')

# LVM active or not
lvm_status=$([ $(lsblk | grep -c "lvm") -gt 0 ] && echo "yes" || echo "no")

# Number of active connections
active_connections=$(ss -Ht state established | wc -l)

# Number of users
users_count=$(users | wc -w)

# Network information
ip_address=$(hostname -I | awk '{print $1}')
mac_address=$(ip link | awk '/ether/ {print $2}')

# Number of sudo commands
sudo_commands=$(journalctl -q _COMM=sudo | grep COMMAND | wc -l)

BANNER="
#######################################################
#                                                     #
#               Welcome to Monitoring!                #
#                                                     #
#######################################################
"

# Display information
wall << EOF
$BANNER
#Architecture: $architecture
#CPU physical: $physical_processors
#vCPU: $virtual_processors
#Memory Usage: $memory_used/$memory_total MB (${memory_percentage}%)
#Disk Usage: $disk_used/$disk_total (${disk_percentage})
#CPU load: $cpu_load
#Last boot: $last_reboot
#LVM use: $lvm_status
#Connections TCP: $active_connections ESTABLISHED
#User log: $users_count
#Network: IP $ip_address ($mac_address)
#Sudo: $sudo_commands cmd
EOF
```
``` bash
chmod +x /path/monitoring.sh
```

## Crontab Configuration
``` bash
sudo crontab -u root -e
    */10 * * * * /path/monitoring.sh
```
### Stop The cron
``` bash
sudo systemctl stop cron
```

# Bonus part

## Setup WordPress

### Installing depencies
``` bash
sudo apt update
sudo apt install -y lighttpd mariadb-server php php-cgi php-mysql php-common php-cli
```

### Configure Lighttpd
``` bash
sudo ufw allow http
sudo ufw status
sudo lighty-enable-mod fastcgi
sudo lighty-enable-mod fastcgi-php
sudo systemctl restart lighttpd
```

### Set Up MariaDB
``` bash
sudo mysql_secure_installation
sudo systemctl start mariadb
sudo systemctl enable mariadb
sudo systemctl status mariadb
sudo mysql_secure_installation
    Enter current password for root (enter for none): <Enter>
    Switch to unix_socket authentication [Y/n]: Y
    Set root password? [Y/n]: Y
    New password: blabla
    Re-enter new password: blabla
    Remove anonymous users? [Y/n]: Y
    Disallow root login remotely? [Y/n]: Y
    Remove test database and access to it? [Y/n]:  Y
    Reload privilege tables now? [Y/n]:  Y
sudo systemctl restart mariadb
```

##### Create db for wordpress && admin user
``` bash
mysql -u root -p
    MariaDB [(none)]> CREATE DATABASE wordpress_db;
    MariaDB [(none)]> CREATE USER 'admin'@'localhost' IDENTIFIED BY 'WPpassw0rd';
    MariaDB [(none)]> GRANT ALL ON wordpress_db.* TO 'admin'@'localhost' IDENTIFIED BY 'WPpassw0rd' WITH GRANT OPTION;
    MariaDB [(none)]> FLUSH PRIVILEGES;
    MariaDB [(none)]> EXIT;
```

### Installing WordPress
``` bash
sudo apt install wget
sudo apt install tar
wget http://wordpress.org/latest.tar.gz
tar -xzvf latest.tar.gz
sudo mv wordpress /var/www/html/
rm -rf latest.tar.gz wordpress/
```

#### Configure Wordpress
``` bash
sudo mv /var/www/html/wp-config-sample.php /var/www/html/wp-config.php
nano /var/www/html/wp-config.php
    <?php
        /* ... */
        /** The name of the database for WordPress */
        define( 'DB_NAME', 'wordpress_db' );

        /** Database username */
        define( 'DB_USER', 'admin' );

        /** Database password */
        define( 'DB_PASSWORD', 'WPpassw0rd' );

        /** Database host */
        define( 'DB_HOST', 'localhost' );
sudo chown -R www-data:www-data /var/www/html/
sudo chmod -R 755 /var/www/html/
sudo systemctl restart lighttpd
```

## Installing Fail2ban
``` bash
sudo apt install fail2ban
sudo systemctl start fail2ban
sudo systemctl enable fail2ban
sudo systemctl status fail2ban
```
### Configure Fail2ban
``` bash
sudo cp /etc/fail2ban/jail.conf /etc/fail2ban/jail.local
nano /etc/fail2ban/jail.local
    [sshd]

    # To use more aggressive sshd modes set filter parameter "mode" in jail.local:
    # normal (default), ddos, extra or aggressive (combines all).
    # See "tests/files/logs/sshd" or "filter.d/sshd.conf" for usage example and details.
    # mode   = normal
    enabled  = true
    maxretry = 3
    findtime = 10m
    bantime  = 1d
    port     = 4242
    logpath  = %(sshd_log)s
    backend  = systemd
sudo systemctl start fail2ban
sudo systemctl enable fail2ban
sudo systemctl status fail2ban
```

### Check banned IPs and unban
``` bash
sudo fail2ban-client status sshd
sudo tail -f /var/log/fail2ban.log
sudo fail2ban-client unban <ip_address>
```