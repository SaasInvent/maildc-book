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

    auto lo
    
    iface lo inet loopback
    iface eth0 inet dhcp
    
    allow-hotplug wlan0
    auto wlan0
    iface wlan0 inet dhcp
        wpa-ssid  "TP-LINK_681998"
        wpa-psk "214A300001192"
    

Then reboot

    shutdown -r now


