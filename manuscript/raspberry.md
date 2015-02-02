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

you need to run the `bin/www` file.

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









