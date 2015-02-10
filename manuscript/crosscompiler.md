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

54.229.137.228:/home/ubuntu/rpikernel/ /media/NFS nfs defaults,user,auto,noatime,intr 0 0

Mounting manually:
------------------

mount 54.229.137.228:/home/ubuntu/rpikernel /media/NFS

Ressources
----------

 - [Le site officiel de 
   crosstool-NG](https://github.com/crosstool-ng/crosstool-ng)
 - [Linaro gcc compiler](http://elinux.org/RPi_Linaro_GCC_Compilation)
 - [General
   introduction](http://www.bootc.net/archives/2012/05/26/how-to-build-a-cross-compiler-for-your-raspberry-pi/)


Kernel modules compilation
--------------------------




[Cross compilation for linux modules](http://www.chicoree.fr/w/Compilation_crois%C3%A9e_d%27un_module_Linux_pour_Rasberry_Pi) can be found here:




