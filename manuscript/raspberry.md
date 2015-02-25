## Raspberry Pi ##

This document holds all the information necessary for a basic Raspberry Pi installation based on the ubuntu linux flavour.

Password
=======


The basic couple identity/password is

 - pi/raspberry

We can change this with

    passwd pi

vsftpd
------

We can install [vsftpd](http://doc.ubuntu-fr.org/vsftpd), which we'll need for CodeAnywhere, with the following command:

    apt-get install vsftpd
    useradd --system ftp 

The config file can be found in the following directory:

    /etc/vsftpd.conf

Copy this file from an existing installation and restart vsftpd.
Attention, you'll need to create /etc/vsftpd/user_list file with user "pi" and "transfert".

Then restart the service from /etc/init.d

.

    ./vsftpd stop
    ./vstpd start




SSH installation
----------------

To install ssh we do the [following:](http://kb.mediatemple.net/questions/1626/Using+SSH+keys+on+your+server)

As user "pi" do the following:

    cd /home/pi
    mkdir ~/.ssh
    ssh-keygen -t rsa -b 2048 -f ~/.ssh/id_rsa -C "raspberry"
    touch ~/.ssh/authorized_keys
    cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys

Then we need to adapt the permissions:

    chmod 700 ~/.ssh && chmod 600 ~/.ssh/*

For further documentation [read this!](http://www.elvenware.com/charlie/development/cloud/SshFtpsPutty.html)


CodeAnywhere
------------

We want to add a SFTP server to CodeAnywhere

    server : raspberry 
    host: saasinvent.ddns.net
    password : optional
    private key : copy id_rsa (cat id_rsa; and copy it) 
    initial directory  : /home/pi

That's all, we're ready to go!

Node Version Manager
--------------------

Node is best used and managed with the [Node Version Manager](https://github.com/creationix/nvm) (NVM)


    apt-get install git

    wget -qO- https://raw.githubusercontent.com/creationix/nvm/v0.23.2/install.sh | bash

    source ~/.nvm/nvm.sh

To install a node version:

    nvm install v0.10.22



How to use NVM
--------------

    nvm use v0.10.22 > /dev/null 2>&1

You need to add the following lines at the end of the user's .bashrc file:


    [[ -s /home/pi/.nvm/nvm.sh ]] && . /home/pi/.nvm/nvm.sh 
    nvm use v0.10.22 > /dev/null 2>&1
Activate everything with the following command:

    source ~/.nvm/nvm.sh


Express install
---------------

[Express](https://github.com/strongloop/express) is a web application framework for Node. It is minimal and flexible. In order to start using Express, you need to use NPM to install the module. Simple type:

    mkdir node
    cd node
    npm install -g express-generator

This will install the Express command line tool, which will aid in creating a basic web application. Once you have Express installed, follow these steps to create an empty Express project:

	cd /home/pi
    mkdir sampersend
    cd sampersend
    express
    npm install

you need to run the `bin/www` file with the `npm start` command.

These commands will create an empty Express project in the directory we just created socketio-test. We then run npm install to get all the dependencies that are needed to run the app. To test the empty application, run node app then navigate your browser to 

    http://saasinvent.ddns.net:3000 

You should get a simple welcome message saying: "Welcome to Express".

If you see the welcome message then you have a basic express application ready and running!


How to send an SMS?
-------------------

We will be using the [this](https://github.com/emilsedgh/modem) node library :

Modem.js allows you to use your GSM modems on node. It offers a very simple API. It supports:

 - Sending SMS messages
 - Receiving SMS messages
 - Getting delivery reports
 - Deleting messages from memory
 - Getting notifications when memory is full
 - Getting signal quality
 - Making ussd sessions
 - 16bit (ucsd messages)
 - 7bit (ascii) messages
 - Multipart messages
 - Getting notifications when someone calls


Our GSM modem
-------------

We will be using the following GSM Modem:

    Cablematic - Modem GSM et GPRS voix (RS-232) 900/1800


USB to RS232
------------

How do we connect our rs232 serial port modem to our usb interface?

[This article](http://blog.mypapit.net/2008/05/how-to-use-usb-serial-port-converter-in-ubuntu.html) is very helpful!

First plug in the USB-Serial Port adaptor to one of your USB ports. Wait for a couple of seconds, then run “dmesg”. You should see these messages at the end of dmesg output.


usb 1-1: new full speed USB device using uhci_and address 2
usb 1-1: configuration #1 chosen from 1 choice

After that, unplug the device and type “lsusb”. You will see a list of outputs similar to this one.

Bus 003 Device 001: ID 0000:0000
Bus 002 Device 007: ID 03f0:4f11 Hewlett-Packard
Bus 002 Device 006: ID 05e3:1205 Genesys Logic, Inc. Afilias Optical Mouse H3003
Bus 002 Device 004: ID 15d9:0a33

Plug in the USB-Serial Port converter back, and run “lsusb” again, and you shall see an additional line, like this.


Bus 003 Device 001: ID 0000:0000
Bus 002 Device 007: ID 03f0:4f11 Hewlett-Packard
**Bus 001 Device 002: ID 4348:5523** --- --- --- *(notice the additional line!)*
Bus 002 Device 006: ID 05e3:1205 Genesys Logic, Inc. Afilias Optical Mouse H3003
Bus 002 Device 004: ID 15d9:0a33

Now we know the vendor id and the product id of the USB-Serial Port converter, this will enable us to load the linux kernel module “usbserial” to activate the device, like this :


    sudo modprobe usbserial vendor=0x1a86 product=0x7523

Run “dmesg” again and you shall see lines similar like these :

    usbserial_generic 1-1:1.0: generic converter detected
    usb 1-1: generic converter now attached to ttyUSB0
    usbcore: registered new interface driver usbserial_generic

As you can see, the new serial port device is mapped to `/dev/ttyUSB0`. You can instruct Ubuntu to load this module automatically by include the line : “usbserial vendor=0x1a86 product=0x7523″ inside “/etc/modules” file.

The USB device is being recognized and set up as /dev/ttyUSB0. When I try setting the baud rate with stty I get:

$ sudo stty -F /dev/ttyUSB0 115200

stty -F /dev/ttyUSB0
speed 9600 baud; line = 0;
min = 1; time = 0;
-brkint -icrnl -imaxbel
-opost -onlcr
-isig -icanon -iexten -echo -echoe -echok -echoctl -echoke


modprobe -r usbserial


dmesg|egrep -i 'serial|ttys'

[Simple script](https://github.com/lurch/rpi-serial-console) to easily enable & disable the Raspberry Pi's serial console. **Disabling the serial console is required if you want to use the Raspberry Pi's serial port (UART) to talk to other devices** e.g. microcontrollers (see http://elinux.org/RPi_Serial_Connection for more information).


Install WIfi adapter
--------------------

The basic documentation can be found [here](http://www.blackmoreops.com/2014/09/18/connect-to-wifi-network-from-command-line-in-linux/)

Step 1: Find available WiFi adapters
------------------------------------

This actually help .. I mean you need to know your WiFi device name before you go an connect to a WiFi network. So just use the following command that will list all the connected WiFi adapters in your Linux machines.

But first install the necessary packages:

sudo -i 
apt-get install libnl-3-dev libnl-genl-3-dev
apt-get install iw

SSID : TP-LINK_681998

Your  /etc/network/interfaces should look like this

    auto wlan0
    
    iface lo inet loopback
    iface eth0 inet dhcp
    
    allow-hotplug wlan0
    iface wlan0 inet manual
    wpa-roam /etc/wpa_supplicant/wpa_supplicant.conf
    iface default inet dhcp
    
Put the following information into your wpa_supplicant.conf


network={
ssid="TP-LINK_681998"
psk="214A300001192"
proto=RSN
key_mgmt=WPA-PSK
pairwise=CCMP
auth_alg=OPEN
}

    wpa-ssid "TP-LINK_681998"
    wpa-psk "214A300001192"

Then reboot

    shutdown -r now



    root@kali:~# iw dev
    phy#1
        Interface wlan0
            ifindex 4
            type managed
    root@kali:~#

Let me explain the output:

This system has 1 physical WiFi adapters.

    Designated name: phy#1
    Device names: wlan0
    Interface Index: 4. Usually as per connected ports (which can be an USB port).
    Type: Managed. 

Type specifies the operational mode of the wireless devices. managed means the device is a WiFi station or client that connects to an access point.

Step 2: Check device status
---------------------------

By this time many of you are thinking, why two network devices. The reason I am using two is because I would like to show how a connected and disconnected device looks like side by side. Next command will show you exactly that.

You can check that if the wireless device is up or not using the following command:

    root@kali:~# ip link show wlan0
    4: wlan0: <BROADCAST,MULTICAST> mtu 1500 qdisc mq state DOWN mode DORMANT qlen 1000
        link/ether 00:60:64:37:4a:30 brd ff:ff:ff:ff:ff:ff
    root@kali:~# 

As you can already see, I got once interface (wlan0) as state UP and wlan1 as state DOWN.

Look for the word “UP” inside the brackets in the first line of the output.

Step 3: Bring up the WiFi interface
-----------------------------------

Use the following command to bring up the WiFI interface

    root@kali:~# ip link set wlan0 up
If you run the show link command again, you can tell that wlan1 is now UP.

    root@kali:~# ip link show wlan0
    4: wlan0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc mq state UP mode DORMANT qlen 1000
        link/ether 00:60:64:37:4a:30 brd ff:ff:ff:ff:ff:ff
    root@kali:~# 

Step 4: Check the connection status
-----------------------------------

You can check WiFi network connection status from command line using the following command

    root@kali:~# iw wlan0 link
    Not connected.
    root@kali:~# 

Step 5: Scan to find WiFi Network
---------------------------------

Scan to find out what WiFi network(s) are detected

    root@kali:~# iw wlan0 scan
    BSS 9c:97:26:de:12:37 (on wlan0)
        TSF: 5311608514951 usec (61d, 11:26:48)
        freq: 2462
        beacon interval: 100
        capability: ESS Privacy ShortSlotTime (0x0411)
        signal: -53.00 dBm 
        last seen: 104 ms ago
        Information elements from Probe Response frame:
        SSID: blackMOREOps
        Supported rates: 1.0* 2.0* 5.5* 11.0* 18.0 24.0 36.0 54.0 
        DS Parameter set: channel 11
        ERP: Barker_Preamble_Mode
        RSN:     * Version: 1
             * Group cipher: CCMP
             * Pairwise ciphers: CCMP
             * Authentication suites: PSK
             * Capabilities: 16-PTKSA-RC (0x000c)
        Extended supported rates: 6.0 9.0 12.0 48.0 
    ---- truncated ----


The 2 important pieces of information from the above are the **SSID** and the security protocol (WPA/WPA2 vs WEP). The SSID from the above example is blackMOREOps. **The security protocol is RSN,** also commonly referred to as WPA2. The security protocol is important because it determines what tool you use to connect to the network.


Step 6: Generate a wpa/wpa2 configuration file
----------------------------------------------

Now we will generate a configuration file for wpa_supplicant that contains the pre-shared key (“passphrase“) for the WiFi network.

    root@kali:~# wpa_passphrase blackMOREOps >> /etc/wpa_supplicant.conf
    abcd1234
    root@kali:~#

(where 'abcd1234' was the Network password)
wpa_passphrase uses SSID as a string, that means you need to type in the passphrase for the WiFi network blackMOREOps after you run the command.


wpa_passphrase will create the necessary configuration entries based on your input. Each new network will be added as a new configuration (it wont replace existing configurations) in the configurations file /etc/wpa_supplicant.conf.

    root@kali:~# cat /etc/wpa_supplicant.conf     
    network={
     ssid="blackMOREOps"
     #psk="abcd1234"     psk=42e1cbd0f7fbf3824393920ea41ad6cc8528957a80a404b24b5e4461a31c820c
    }
    root@kali:~# 

 

Step 7: Connect to WPA/WPA2 WiFi network
----------------------------------------

Now that we have the configuration file, we can use it to connect to the WiFi network. We will be using wpa_supplicant to connect. Use the following command

    root@kali:~# wpa_supplicant -B -D wext -i wlan0 -c /etc/wpa_supplicant.conf
   

Where,

 - B means run wpa_supplicant in the background
 - D specifies the wireless driver. wext is the generic driver
 - c specifies the path for the configuration file.

Use the iw command to verify that you are indeed connected to the SSID.

    root@kali:~# iw wlan0 link
    Connected to 9c:97:00:aa:11:33 (on wlan0)
        SSID: blackMOREOps
        freq: 2412
        RX: 26951 bytes (265 packets)
        TX: 1400 bytes (14 packets)
        signal: -51 dBm
        tx bitrate: 6.5 MBit/s MCS 0
    
        bss flags:    short-slot-time
        dtim period:    0
        beacon int:    100

 

Step 8: Get an IP using dhclient
--------------------------------

Until step 7, we’ve spent time connecting to the WiFi network. Now use dhclient to get an IP address by DHCP

    root@kali:~# dhclient wlan0
    Reloading /etc/samba/smb.conf: smbd only.
    root@kali:~#

You can use ip or ifconfig command to verify the IP address assigned by DHCP. The IP address is 10.0.0.4 from below.

    root@kali:~# ip addr show wlan0
    4: wlan0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc mq state UP qlen 1000
        link/ether 00:60:64:37:4a:30 brd ff:ff:ff:ff:ff:ff
        inet 10.0.0.4/24 brd 10.0.0.255 scope global wlan0
           valid_lft forever preferred_lft forever
        inet6 fe80::260:64ff:fe37:4a30/64 scope link 
           valid_lft forever preferred_lft forever
    root@kali:~# 

(or)

    root@kali:~# ifconfig wlan0
    wlan0 Link encap:Ethernet HWaddr 00:60:64:37:4a:30 
     inet addr:10.0.0.4 Bcast:10.0.0.255 Mask:255.255.255.0
     inet6 addr: fe80::260:64ff:fe37:4a30/64 Scope:Link
     UP BROADCAST RUNNING MULTICAST MTU:1500 Metric:1
     RX packets:23868 errors:0 dropped:0 overruns:0 frame:0
     TX packets:23502 errors:0 dropped:0 overruns:0 carrier:0
     collisions:0 txqueuelen:1000 
     RX bytes:22999066 (21.9 MiB) TX bytes:5776947 (5.5 MiB)
    root@kali:~# 

Add default routing rule.The last configuration step is to make sure that you have the proper routing rules.

    root@kali:~# ip route show 
    default via 10.0.0.138 dev wlan0 
    10.0.0.0/24 dev wlan0  proto kernel  scope link  src 10.0.0.4 

 

Connect to WiFi network in Linux from command line - Check Routing and DHCP - blackMORE Ops - 8

 

Step 9: Test connectivity
-------------------------

Ping Google’s IP to confirm network connection (or you can just browse?)

    root@kali:~# ping 8.8.8.8
    PING 8.8.8.8 (8.8.8.8) 56(84) bytes of data.
    64 bytes from 8.8.8.8: icmp_req=3 ttl=42 time=265 ms
    64 bytes from 8.8.8.8: icmp_req=4 ttl=42 time=176 ms
    64 bytes from 8.8.8.8: icmp_req=5 ttl=42 time=174 ms
    64 bytes from 8.8.8.8: icmp_req=6 ttl=42 time=174 ms
    ^C
    --- 8.8.8.8 ping statistics ---
    6 packets transmitted, 4 received, 33% packet loss, time 5020ms
    rtt min/avg/max/mdev = 174.353/197.683/265.456/39.134 ms
    root@kali:~# 

 

Summary
-------

This is a very detailed and long guide. Here is a short summary of all the things you need to do in just few line.

    root@kali:~# iw dev
    root@kali:~# ip link set wlan0 up
    root@kali:~# iw wlan0 scan
    root@kali:~# wpa_passphrase blackMOREOps >> /etc/wpa_supplicant.conf
    root@kali:~# wpa_supplicant -i wlan0 -c /etc/wpa_supplicant.conf
    root@kali:~# iw wlan0 link
    root@kali:~# dhclient wlan0
    root@kali:~# ping 8.8.8.8
    (Where wlan0 is wifi adapter and blackMOREOps is SSID)
    (Add Routing manually)
    root@kali:~# ip route add default via 10.0.0.138 dev wlan0

At the end of it, you should be able to connect to WiFi network. Depending on the Linux distro you are using and how things go, your commands might be slightly different. Edit commands as required to meet your needs.
