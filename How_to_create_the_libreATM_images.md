# How to create the libreATM images #

> Santiago Crespo 2015 WTFPL

## Install libreATM ##

Follow this instructions [How to install libreATM on a Skyhook from scratch](How_to_Install_libreATM_on_a_Skyhook_from_scratch.md) but ignore the "Expand Filesystem" step.

## Generate the image ##

Change /dev/mmcblk0 to the name of your actual SD card device:
    sudo -s
    SD_CARD=/dev/mmcblk0
    mount | grep $SD_CARD | awk '{print "umount "$1}' | sh
    dd if=$SD_CARD of=libreATM_0.1-EUR.img bs=1024 count=3081016


