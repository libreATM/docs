# How to install libreATM on a Skyhook from an image #

> Santiago Crespo 2015 WTFPL

This instructions assume that you have root access to a Linux machine and basic Linux command-line skills.

It is possible to do this from Windows using Win32DiskImager to burn the image and putty to connect using ssh.

## Burning the image to the SD ##
Insert the SD card on your computer (4Gb or more. Better if class 10).

Find the device name (examples: /dev/sdb /dev/mmcblk0 )
~~~ 
    fdisk -l | grep "Disk /dev"
~~~
**CHANGE /dev/device** to the actual device name!
~~~
    SD_CARD=/dev/device
# Umount all SD_CARD partitions
    mount | grep $SD_CARD | awk '{print "umount "$1}' | sh

# Download and extract image:
	IMAGE=libreATM_0.1-EUR.img
    wget https://github.com/libreATM/libreATM-images/raw/master/libreATM-images/$IMAGE.7z
    7z x $IMAGE.7z
    rm $IMAGE.7z

# Burn it!
    dd bs=1024 if=$IMAGE of=$SD_CARD
~~~

## Expand filesystem and internationalisation ##
Boot the Raspberry and connect to it using ssh.
User: pi
Password: password
~~~
    sudo -s
    raspi-config
~~~
* Expand Filesystem
* Internationalisation Options > Change Locale
* Internationalisation Options > Change Timezone
* Internationalisation Options > Change Keyboard Layout
* Finish
* reboot? yes

## Change passwords ##
Change "pi" user password
~~~
    password
~~~
**Change** *new-password* for the actual new mysql root password
~~~
    echo 'update user set Password=PASSWORD("new-password") WHERE User="root";flush privileges;' > /tmp/mysqlpass.sql
    mysql -u root --password=password mysql < /tmp/mysqlpass.sql
    rm /tmp/mysqlpass.sql
~~~
## ssh keys ##
Now we are going to regenerate the OpenSSH host keys. Connect a mouse and move it (I hope that it does something)
~~~
    rm -f /etc/ssh/ssh_host_* ; dpkg-reconfigure openssh-server
~~~
Disconnect and try to connect again using ssh, remove the offending key line on .ssh/known_hosts and connect again.

##  Firmware update ##
~~~
    sudo -s
    wget https://raw.github.com/Hexxeh/rpi-update/master/rpi-update -O /usr/bin/rpi-update
    chmod +x /usr/bin/rpi-update
    rpi-update
    reboot
~~~
##  System update ##
Update, dist-upgrade and clean:
~~~
    aptitude update && aptitude dist-upgrade -y && aptitude clean
~~~

## Tablet set-up ##
Continue with the [tablet set-up](How_to_Install_libreATM_on_a_Nexus7_from_scratch.md)


