# How to localize libreATM #

## System Internationalisation options ##
Connect to the raspberry using ssh.
~~~
    sudo -s
    raspi-config
~~~
* Internationalisation Options > Change Locale
* Internationalisation Options > Change Timezone
* Internationalisation Options > Change Keyboard Layout
* Finish

## ATM Currency ##
Change USD to match your currency
~~~
    CURRENCY=USD
    /var/www/btc/cli install-currency USD
    sed -i.eur 's/EUR/'$CURRENCY'/g' /var/www/btc/assets/js/index.js
    sed -i.eur 's/EUR/'$CURRENCY'/g' /var/www/btc/pages/purchase.phtml
    sed -i.eur 's/EUR/'$CURRENCY'/g' /var/www/btc/includes/PricingProviders/Kraken.php
    sed -i.eur 's/EUR/'$CURRENCY'/g' /var/www/btc/includes/PricingProviders/BitcoinAverage.php
    sed -i.eur 's/EUR/'$CURRENCY'/g' /var/www/btc/includes/PricingProviders/LocalBitcoins.php
    reboot
~~~
