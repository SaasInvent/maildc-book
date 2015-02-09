Cross-compilation
-----------------

This is the document that describes the steps necessary steps for cross-compilation:


[Cross compilation](http://www.chicoree.fr/w/Compilation_crois%C3%A9e_facile_pour_Raspberry_Pi) may be done from Amazon AWS.

http://superuser.com/questions/577124/how-to-connect-to-aws-ec2-instance-from-chromebook-pixel

http://www.vkick.com/?p=261

Create an Amazon AMI
--------------------

First import keys from "ssh-keys" directory
This is already done, you do this once

Choose "keys" as private/public key pair.



Secure Shell
------------
You must import two files for each identity. One should be the private key
and should not have a file extension. The other should be the public key,
and must end in “.pub”. 
For example, “keys” and “keys.pub”.


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




