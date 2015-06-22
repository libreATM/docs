# Skyhook Power Supply #

> Santiago Crespo 2015 WTFPL

If you want to change the power supply and/or the DC-DC step-down, keep in mind the max consumptions of each part:

~~~
Tablet............2 A
Bill validator....1.5 A
Raspberry pi......1 A
------------------------
       TOTAL MAX..4.5 A
~~~

Got from ebay a 12V 5A power supply and a cheap DC-DC step down 5V 3A to power the raspberry and the tablet with a hacked USB cable. USB data lines go from the tablet to the raspberry and USB power lines goes from the tablet to the DC-DC step-down converter. There are other alternatives like using a powered USB HUB instead of the hacked cable.

~~~
 __________
|    o     |
|          |<-------------- USB POWER (+) ------------------------------------|
|          |                                            ___________           |   ___________         __________
| Tablet   |<-------------- USB DATA ----------        | Raspberry |          |  |  DC-DC    |12v     |  AC/DC  |===== 230 V AC
|          | microusb                          |=======|           |microusb  |5v| step-down |========| 12V 5A  |
|          |<-------------- USB DATA ----------     usb|    pi     |=============| 5v  3A    |    ||  |_________|
|          |                                           |___________|          |  |___________|    ||
|          |                                                                  |                   ||
|          |<-------------- USB POWER (-) ------------------------------------|                   ||
|__________|                                                                                      ||12v
                                                                                             -------------
                                                                                             |   bill    |
                                                                                             | validator |
                                                                                             -------------
~~~

The CyanogenMod stock kernel does not come with force_fast_charge support. I don't know if the skyhook "factory default" kernel comes with it. That means that right now, when the tablet detects a computer like the raspberry pi on the other side it won't suck more than 500mA from the USB port and we cannot force the fast charge mode. And the stock Asus charger is 2000mA.

A workaround for that problem is to enable the powersave CPU governor:

* Settings > Performance > Processor > CPU Governor > powersave

And set brightness to low/medium.

This way the tablet is able to keep the battery at full charge.
