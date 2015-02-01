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


SSH installation

To install ssh we do the [following:](http://kb.mediatemple.net/questions/1626/Using+SSH+keys+on+your+server)


    cd /home/pi
    mkdir ~/.ssh
    ssh-keygen -t rsa -b 2048 -f ~/.ssh/id_rsa -C "raspberry"

Then we need to adapt the permissions:

    chmod 700 ~/.ssh && chmod 600 ~/.ssh/*




