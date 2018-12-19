# OpenWrt-repository

# Prepare the repository

* compile and install usign on the server - https://github.com/xypron/usign
* cd /opt and git clone https://github.com/xypron/usign.git
* cd usign
* mkdir build
* cd build
* cmake ..
* make
* sudo make install
* cd /opt and generate key run the command - "usign -G -p openWrtUsign.pub -s openWrtUsign.key"
* create the ipk directory for example, /opt/ipk
* download the file to the directory - https://gitlab.labs.nic.cz/turris/openwrt/raw/test/scripts/ipkg-make-index.sh
* create makeindex.sh and copy & paste below codes and save it and chmod +x makeindex.sh
```
#!/bin/bash
SCRIPT="./ipkg-make-index.sh"
KEY="/opt/openWrtUsign.key"
# Generates package manifest
$SCRIPT . 2>/dev/null > Packages.manifest
grep -vE '^(Maintainer|LicenseFiles|Source|Require)' Packages.manifest > Packages
gzip -9nc Packages > Packages.gz
usign -S -m Packages -s $KEY
```
* move ipk files to /opt/ipk
* cd /opt/ipk and run "./makeindex.sh"
* mkdir /opt/openwrt-repo moves ipk files, Packages, Packages.gz, Packages.manifest and Packages.sig and openWrtUsign.pub  to the /opt/openwrt-repo
* copy the whole directory to a webserver

# On the device side

* ssh into the device and cd /tmp
* wget https://url/to/ openWrtUsign.pub
* opkg-key add openWrtUsign.pub
* echo 'src/gz bec-feed https:/url/to/ ' >> /etc/opkg/customfeeds.conf
* opkg update
* opkg install <package name>
