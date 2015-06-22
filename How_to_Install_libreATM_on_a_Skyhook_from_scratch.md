# How to install libreATM on a Skyhook from scratch #

> Santiago Crespo 2015 WTFPL

## Burning the image to the SD ##
Insert the SD card on your computer (4Gb or more. Better if class 10).
~~~
    cd /tmp/
    wget https://raw.githubusercontent.com/kresp0/rpi-scripts/master/raspbian_to_sd.sh
~~~
Do not run a random script as root without review:
~~~
    more raspbian_to_sd.sh
    sudo -s
    chmod +x raspbian_to_sd.sh
    ./raspbian_to_sd.sh
~~~

## First Boot ##
Insert the SD card on the raspberry and boot with display+keyboard connected.

raspi-config is loaded on first boot. Use this options:

* Expand Filesystem
* Change User Password
* Set Internationalisation Options -> Change Timezone
* Set Internationalisation Options -> Change keyboard layout
* **OPTIONAL** Set Overclock to Turbo (use heat sink and a good power supply)
* Advanced Options > Hostname > libreATM
* Advanced Options > Memory Split to 16
* Finish
* Reboot? yes

## System configuration ##
* Unplug the keyboard and display. Plug the tablet and bill validator USB cables.
* Connect to the raspberry using ssh.

###  Firmware update ###
~~~
    sudo -s
    wget https://raw.github.com/Hexxeh/rpi-update/master/rpi-update -O /usr/bin/rpi-update
    chmod +x /usr/bin/rpi-update
    rpi-update
    reboot
~~~

###  Initial system config ###
We need root
~~~
    sudo -s
~~~

Remove rpcbind, nfs-common, wolfram and other propietary software (667 MB):
~~~
    aptitude purge -y rpcbind wolfram-engine oracle-java8-jdk pistore
~~~

Remove proprietary wireless firmware:
~~~
    aptitude purge -y firmware-atheros firmware-brcm80211 firmware-libertas  firmware-ralink firmware-realtek
~~~

Disable unwanted repos:
~~~
    mv /etc/apt/sources.list.d/collabora.list /etc/apt/sources.list.d/collabora.list.disabled
    sed -i.bak 's/non-free //g' /etc/apt/sources.list
~~~
Update, dist-upgrade and clean:
~~~
    aptitude update && aptitude dist-upgrade -y && aptitude clean
~~~

Install and run Virtual Richard M. Stallman to check if there are any non-free software installed:
~~~
    aptitude install -y vrms
    vrms
~~~

Automatic security updates. Automatically download and install stable updates? -> Yes
~~~
    aptitude install -y unattended-upgrades && dpkg-reconfigure -plow unattended-upgrades
~~~

Now we are going to regenerate the OpenSSH host keys. Connect a mouse and move it (I hope that it does something)
~~~
    rm -f /etc/ssh/ssh_host_* ; dpkg-reconfigure openssh-server
~~~

Disconnect and try to connect again using ssh, remove the offending key line on .ssh/known_hosts and connect again.

~~~
    sudo -s
    rm /etc/motd
    nano /etc/motd
~~~

~~~

 _ _ _                _   _____       
| (_) |__  _ __ ___  /_\ /__   \/\/\  
| | | '_ \| '__/ _ \//_\\  / /\/    \ 
| | | |_) | | |  __/  _  \/ / / /\/\ \
|_|_|_.__/|_|  \___\_/ \_/\/  \/    \/
                                      
~~~    

## ATM SERVER ##
###  USB tethering ###
~~~
    echo "
    # USB network configuration
    iface usb0 inet static
    address 192.168.42.45
    netmask 255.255.255.0
    #gateway 192.168.42.129
    dns-nameservers 8.8.8.8
    " >> /etc/network/interfaces
~~~
    # Enable interfaces on the correct order and do some other things at boot
~~~
    sed -ri.old 's/^exit 0/ifdown usb0\nifdown eth0\nifup eth0\nifup usb0\nrm \/tmp_disk\/* 2\>\/dev\/null\n\nexit 0/g' /etc/rc.local
~~~
### LAMP install ###
When asked, put a password for the MySQL root. You'll need it later.
~~~
    apt-get install -y apache2 libapache2-mod-php5 mysql-server php5-mysql php5-curl python-crypto php5-mcrypt
~~~
###  Apache config ###
Download libreATM server
~~~
    cd /root
    git clone https://github.com/libreATM/server/
    mkdir /var/www/btc
    mv /root/server/* /var/www/btc/
    perl -pe 's/\*/192.168.42.45/g' /etc/apache2/ports.conf  > /tmp/p ; mv /tmp/p /etc/apache2/ports.conf
    mv /var/www/btc/libreATM.apache /etc/apache2/sites-available/libreATM
~~~

Create the logs directory:
~~~
    mkdir /home/pi/phplog
    chown www-data /home/pi/phplog
~~~

Activate site and mod_rewrite, restart apache:
~~~
    a2dissite default
    a2ensite libreATM
    a2enmod rewrite
    php5enmod mcrypt
	echo "Buy some Bitcoin" > /var/www/index.html
    service apache2 restart
~~~

Install font-awesome
~~~
    cd /var/www/btc/assets/
    wget https://fortawesome.github.io/Font-Awesome/assets/font-awesome-4.3.0.zip
    unzip font-awesome-4.3.0.zip
    rm -rf font-awesome
    mv font-awesome-4.3.0 font-awesome
~~~

Create /tmp_disk/
~~~
    mkdir -p /tmp_disk
~~~

Allow www-data to access the usb-serial port, webserver directories and shutdown command:
~~~
    addgroup www-data dialout
    chown -R www-data: /var/www/btc
    chown www-data /tmp_disk
    chmod u+s /sbin/shutdown
~~~

###  MySQL config ###
Create a new skyhook user without password and a database, here you need the MySQL root user password:
~~~
    cd /var/www/btc/database
    mysql -u root -p -h localhost < libreATM-database.sql
    mysql -u root -p -h localhost < libreATM-tables.sql
~~~

### Currency ###
If your ATM use another currency other than the euro, you have to follow the [ATM Currency section on the "How to localize libreATM document"](How_to_localize_libreATM.md)

## Tablet setup ##
Continue with the [tablet setup](How_to_Install_libreATM_on_a_Nexus7_from_scratch.md)

