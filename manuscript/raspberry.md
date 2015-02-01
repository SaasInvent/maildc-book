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

    apt-get install git

    wget -qO- https://raw.githubusercontent.com/creationix/nvm/v0.23.2/install.sh | bash

source ~/.nvm/nvm.sh

Pour l’installation de la dernière version de node (Current Version: v0.10.22), il convient de saisir la commande suivante:

nvm install v0.10.22



Pour son utilisation

nvm use v0.10.22 > /dev/null 2>&1
Il faut rajouter ces deux lignes dans le .bashrc de l’utilisateur!
[[ -s /root/.nvm/nvm.sh ]] && . /root/.nvm/nvm.sh 
nvm use v0.10.22 > /dev/null 2>&1

Puis pour activer le paramétrage:ggg

source ~/.nvm/nvm.sh



