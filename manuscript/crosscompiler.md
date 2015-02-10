Cross-compilation
-----------------

This is the document that describes the steps necessary steps for cross-compilation:


[Cross compilation](http://www.chicoree.fr/w/Compilation_crois%C3%A9e_facile_pour_Raspberry_Pi) may be done from Amazon AWS. For this we will be using two machines : one for cross-compiling and another to hold the kernel sources.





Create an Amazon AMI
--------------------

First import keys from "ssh-keys" directory. (This is already done, you only do this once)

Choose **"keys"** as private/public key pair for your Amazon instance.



Secure Shell
------------
[You must import two](http://www.vkick.com/?p=261) files for each identity. One should be the private key and should not have a file extension. The other should be the public key, and must end in “.pub”. 
For example, *“keys” and “keys.pub”.*

For more information, have a look [here.](http://superuser.com/questions/577124/how-to-connect-to-aws-ec2-instance-from-chromebook-pixel)



Network File system
-------------------

Unfortunately, the kernel sources are too big in size for one AWS instance. The simple solution is to use two AWS instances and then mount one onto the other...

We will be using two file servers:


    crosscompiling : 54.229.137.228
    rpikernel : 54.77.161.209

We need to open some ports on ASW security groups:
Through the EC2 Console or API, you want to allowing connections from your client to your server on the following ports:

    TCP: 111, 2049
    UDP: 111, 32806

Configuration on the server (Ubuntu):
-------------------------------------

    sudo apt-get install nfs-kernel-server
    sudo apt-get install nfs-utils rpcbind

Configuration is done in the following file:
`/etc/exports`

 Please add the following line (adapt to your server IP)


    /home/ubuntu/rpikernel 54.229.137.228/24(rw,all_squash,anonuid=1000,anongid=1000,sync,no_subtree_check)

    exportfs -ar
    sudo service nfs-kernel-server reload



Configuration on the client
---------------------------

You'll need to install the nfs-common packet:

    apt-get install nfs-common

    sudo mkdir /media/NFS

Then edit the /etc/fstab file and add the following line:

    54.77.161.209:/home/ubuntu/rpikernel/ /media/NFS nfs defaults,user,auto,noatime,intr 0 0

Mounting manually:
------------------

On Amazon AWS, start rpikernel instance server first, then from instance server crosscompiling :

    mount 54.77.161.209:/home/ubuntu/rpikernel /media/NFS

Ressources
----------

 - [Le site officiel de 
   crosstool-NG](https://github.com/crosstool-ng/crosstool-ng)
 - [Linaro gcc compiler](http://elinux.org/RPi_Linaro_GCC_Compilation)
 - [General
   introduction](http://www.bootc.net/archives/2012/05/26/how-to-build-a-cross-compiler-for-your-raspberry-pi/)



Cross-tool installation
-----------------------

Prerequisites

Launch the following command as root:

    apt-get install bzip2 build-essential bison flex gperf texinfo gawk libtool automake libncurses5-dev subversion

Crosstool-ng

In order to install crosstool-ng do the following: 

    export VERSION=1.20.0
    wget http://crosstool-ng.org/download/crosstool-ng/crosstool-ng-${VERSION}.tar.bz2
    tar xjf crosstool-ng-${VERSION}.tar.bz2
    cd crosstool-ng-${VERSION}
    ./configure --prefix=/opt/crosstool-ng
    make
    sudo make install

Configuration
-------------

For detailed instructions, [have a look here](http://www.chicoree.fr/w/Compilation_crois%C3%A9e_facile_pour_Raspberry_Pi)!

The following variable may also be declared into .bashrc:

    export PATH="${PATH}:/opt/crosstool-ng/bin"



Working directory
-----------------

    mkdir rpi-dev
    cd rpi-dev

Now launch the tool-chain configuration

    ct-ng menuconfig
Toolchain build and test

After the different configuration steps, simply type:

    ct-ng build
    export PATH="${PATH}:/home/ubuntu/x-tools/arm-unknown-linux-gnueabi/bin"

The last line may also be added to .bashrc.

To test the toolchain, do the following


Kernel module compilation
--------------------------




[Cross compilation for linux modules](http://www.chicoree.fr/w/Compilation_crois%C3%A9e_d%27un_module_Linux_pour_Rasberry_Pi) can be found here:

RPI Kernel
----------

Change to your home directory, then:

git clone http://github.com/raspberrypi/linux rpi-kernel

En plus des sources à proprement parler, vous avez besoin de la configuration du noyau. Celle-ci peut être directement récupérée dans le pseudo-système de fichiers /proc de la cible:

    cd /media/NFS
    ssh pi@saasinvent.ddns.net cat /proc/config.gz | gunzip - > /media/NFS/.config



Mais les sources et la configuration ne suffisent pas. Il faut aussi compiler le noyau. Ce n'est pas compliqué; juste long…

    cd /media/NFS 
    make ARCH=arm CROSS_COMPILE=arm-unknown-linux-gnueabi- oldconfig
    make ARCH=arm CROSS_COMPILE=arm-unknown-linux-gnueabi- prepare

L'option -j4 indique au make de lancer jusqu'à 9 processus en parallèle.

À ajuster en fonction du nombre de "cœurs" de votre machine

    make -j4 ARCH=arm CROSS_COMPILE=arm-unknown-linux-gnueabi-


Compiler son propre module

Une fois les outils en place, écrire son propre module revient à écrire un programme C contenant les points d'entrée init_module (appelé par le kernel au chargement du module) et cleanup_module (appelé par le kernel au déchargement du module):

    #include <linux/module.h>  /* Needed by all modules */
    MODULE_LICENSE("GPL");
    MODULE_AUTHOR("sylvain@chicoree.fr, 2012");
    MODULE_DESCRIPTION("Demo module for cross compiling");
     
    int init_module(void)
    {
      // Print a message in the kernel log
      printk("Hello world\n");
     
      // A non 0 return means init_module failed; module can't be loaded.
      return 0;
    }

 
 

    void cleanup_module(void)
    {
      // Print a message in the kernel log
      printk("Goodbye world\n");
    }

La construction du module à partir de ce fichier source se faisant avec les makefiles spécifiques du kernel, l'usage est d'utiliser un fichier Makefile local au répertoire du module, et qui se contente d'invoquer de manière récursive ses homologues présents dans le dossier du kernel:



My Module Makefile
------------------


obj-m := my-module.o
KDIR := /media/NFS/
PWD := $(shell pwd)
all:
	$(MAKE) -C $(KDIR) M=$(PWD) modules
clean:
	$(MAKE) -C $(KDIR) M=$(PWD) clean

Pensez à modifier dans le fichier ci-dessus la ligne KDIR:= pour refléter l'emplacement des sources du kernel sur votre machine. Puis, lors de l'invocation du make, n'oubliez pas de définir les macros ARCH et CROSS_COMPILE adéquate pour votre cible:

Construction du module
----------------------

    make ARCH=arm CROSS_COMPILE=arm-unknown-linux-gnueabi-

Copy to raspberry
-----------------


    scp my-module.ko pi@saasinvent.ddns.net:.


Pour tester, il suffit de charger/décharger le module du noyau de la cible, chacune de ces opération faisant apparaitre un message dans le journal du système (grâce aux printk présents dans le code source):

Test
----


    ssh pi@saasinvent.ddns.net
    sudo insmod my-module.ko
    dmesg
    [...]
    [69370.606868] Hello world
    sudo rmmod my_module
    dmesg
    [...]
    [69370.606868] Hello world
    [69411.891597] Goodbye world








