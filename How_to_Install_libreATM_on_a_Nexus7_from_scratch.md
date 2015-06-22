# LibreATM Nexus 7 set-up #

> Santiago Crespo 2015 WTFPL

## CyanogenMod ##
* [Download CyanogenMod](https://download.cyanogenmod.org/?device=grouper)
* Follow the [install instructions](http://wiki.cyanogenmod.org/w/Install_CM_for_grouper)

## CyanogenMod Settings ##
* Settings > More > Airplane mode
* Settings > More > USB tethering (on)
* Settings > Status bar > Battery percentage > Next to the icon
* Settings > Notification drawer > Select and order tiles > Add + > USB tether
* Settings > Location (off)
* Settings > Security > Unknown sources (on)
* Settings > Lock screen > Screen lock > None
* Settings > About tablet > (tap several times on build number)
* Settings > Developer options > Stay awake
* Settings > Developer options > Root access > Apps and ADB
* Settings > Developer options > Android Debugging (on)
* Settings > Display & lights > Adaptative Brightness (off)
* Settings > Display & lights > Brightness level > middle (or low)
* Settings > Performance > Processor > CPU Governor > powersave
* Settings > Performance > Processor > Set on boot (on)
* Remove all icons from home screen. Add the browser icon.

## Manual shutdown ##
* Install [Shutdown - be.ppareit.shutdown](https://f-droid.org/repository/browse/?fdid=be.ppareit.shutdown)
* Add icon to home screen

## Automatic shutdown ##
* Install [AutoPowerOff - com.yschi.autopoweroff](https://play.google.com/store/apps/details?id=com.yschi.autopoweroff)
* Configure autopoweroff to shutdown 1 minute after the cable unplug

## Automatic power-on ##
Connect to the raspberry pi
~~~
sudo -s
apt-get install p7zip-full
~~~
Download [adb_fastboot_pi.7z](http://forum.xda-developers.com/showthread.php?t=2047891)

~~~
cd /usr/local/bin
wget https://github.com/libreATM/libreATM-adb-rpi/raw/master/adb_fastboot_pi.7z
7z x adb_fastboot_pi.7z
rm adb_fastboot_pi.7z
~~~

Reboot the tablet into recovery mode. From the raspberry, do:
~~~
adb reboot recovery
~~~
On the tablet mark "Always allow from this computer" and click OK

Unlock bootloader, press the power button once to select "yes"
~~~
fastboot oem unlock
~~~

Sets the device to auto power on instead of charging:
~~~
fastboot oem off-mode-charge 0
~~~

Reboot the tablet.

* Settings > More > USB tethering (on)

## Browser ##
* Open the browser
* Settings > General > Set homepage > Most visited sites
* Visit http://192.168.42.45/ and http://192.168.42.45/off

## ATM configuration ##
* Create a gmail.com account
* Create a blockhain.info account
* Open the browser, go to http://192.168.42.45/admin/setup
* Configure the ATM using the web interface

## Kiosk ##
* Install [Kiosk Browser Lockdown - com.procoit.kioskbrowser](https://play.google.com/store/apps/details?id=com.procoit.kioskbrowser)
* Always Kiosk Browser
* press 4 times fast on the screen, default password is 0000
* Settings > General > Kiosk URL > http://192.168.42.45
* Settings > General > Kiosk Title > "Buy Bitcoin here"
* Settings > General > Idle Page Timeout (off)
* Settings > Hardware > Use Device Back Button (off)
* Settings > Admin > Settings Password

## Tests and fun ##
* Do the [Shutdown procedure.](https://github.com/libreATM/docs/blob/master/docs/How_to_Power_ON_and_Shutdown_a_libreATM.md)
* Do the [Power-ON procedure.](https://github.com/libreATM/docs/blob/master/docs/How_to_Power_ON_and_Shutdown_a_libreATM.md)
* Buy some Bitcoin using fiat, send me a tip if it works ;) [14qpgjQmWSnHbrZKLTJCvhX9sCpRgr1Hw2](https://blockchain.info/address/14qpgjQmWSnHbrZKLTJCvhX9sCpRgr1Hw2)


## Things that do not work ATM ##

### Automatic USB tethering ###

#### **THIS DOESN'T WORK** Automatic USB tethering using jonhannis tether.apk ####
* Install and open [tether.apk](http://goo.gl/KKsg7N)
* Select 'Enable!' > 'Remember my choice' > 'Allow'
* Reboot the tablet.

#### **THIS DOESN'T WORK** Automatic USB tethering using a init script ####
* Copy this script to /data/local/userinit.sh on the tablet and do chmod 555 /data/local/userinit.sh

~~~
\#!/system/bin/sh 
setprop sys.usb.config rndis,adb
service call connectivity 30 i32 1
ifconfig rndis0 192.168.42.1
~~~    

