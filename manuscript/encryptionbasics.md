
#Encryption Basics

This chapter covers the basic security aspects of MailDC.


Encryption in MailDc
--------------------

Two types of encryption are used in MailDc

 - symetric key encryption and 
 - asymetric key encryption

The two types of encryption serve two differents objectives:

Symetric key encryptions is used for large data encryption.
 Asymetric key encryption is used to encrypt the symetric key!

Encryption in MailDC is always a two step process :

 - first you encrypt your data (messages, agenda, contacts)
 - then you encrypt the key you used in the first step





Symetric key encryption
-----------------------

Symetric key encryption in MailDC is done with the “Advanced Encryption Standard” (AES). The encryption is done directly on the browser with a unique encryption key that is created on the fly everytime MailDC needs to encrypt a message. To encrypt our data (messages, agenda, contacts) we are using the following libraries


 - [jsaes](http://point-at-infinity.org/jsaes/) (probably not used)
 - [forge#rsa](http://point-at-infinity.org/jsaes/)



Here is how you would do data encryption with jsaes:


>AES_Init();

>var block = new Array(16);
for(var i = 0; i < 16; i++)
  block[i] = 0x11 * i;

>var key = new Array(32);
for(var i = 0; i < 32; i++)
  key[i] = i;

>AES_ExpandKey(key);
AES_Encrypt(block, key);

>AES_Done();


Every message is encrypted with a unique key generated as follows:

>function generateHexString(length) { 
var ret = ""; 
while (ret.length < length) { 
ret += Math.random().toString(16).substring(2);
} 
return ret.substring(0,length); 
} 

The toString() method parses its first argument, and attempts to return a string representation in the specified radix (base). For example, for hexadecimal numbers (base 16)

To create a 256 bit key your would call this function as follows
256-bit 64 digit key $> alert("256-bit:" + generateHexString(64));


Asymetric encryption
--------------------

Once our data has been encrypted, we need to encrypt the key as well, otherwise our data will not be encrypted for very long…

Asymetric key encryption works with two keys


 - a public key 
 - private key

The public key is used to encrypt the symetric key, the private key is used to decrypt the symetric key.

MailDC will probably use the Forge library for all encryption aspects. be it symetric or asymetric key encryption.

Another option for rsa encryption would have been:

node-rsa


Encryption workflow in MailDC
-----------------------------

MailDC uses asymetric key encryption. One of the problems with asymetric key encryptions is the distribution of the public and above all of the private key. 
How does one asssure that no evil minded person intercepts our private key during delivery? Or worse, makes a copy of it, modifies it?

The answer to that question is, you simply don’t deliver the private key!

MailDC will create the private/public key-pair on the browser and will only send back to the server the public key. The private key will never be sent over the Internet! The private key will be stored on the client computer. That’s it!

The creation of the private/public key pair is done when a user signs up for MailDC. After confirmation of the sign-up message, the key-pair is created and the public key is sent back to the server.

Harry writes a letter to Sally

Once Harry has signed in to MailDC, he starts to write a letter to Sally. As soon as he clicks on the “Send” button, MailDC takes over. 

This is a confidential affair and no-one can be too carefull... First, MailDC will create a unique encryption key for AES encryption and then encrypt his love-letter with that key.

MailDC will then claim from the server Sally’s public key. MailDC will then, on the browser,  encrypt the symetric key with Sally’s public key.
The encrypted symetric key together with the encrypted message will then be sent to Sally.

Once Sally receives Harry’s message she then can decrypt it using the key that Harry has attached to his letter. But before she can do that, she has to decrypt the symetric key with her private key. Once she has decrypted the symetric key, she can use that key to decrypt Harry’s message.

**It goes without saying that it is actually MailDC that is doing all the encryption and decryption…**


Message database encryption
---------------------------

All the messages are stored in a file-based database. MailDC uses what is called a “local shared object” or “flash-cookie” (a Macromedia/Adobe technologie).

In the mail delivery process, so far in our discussion, we have found a solution for two threats:

the node (server) : no messages on the server
the connecion : everything is encrypted

Which leaves us with a third threat wich is the client box. What if your laptop gets stolen?

To address this problem, MailDC needs to encrypt the data stored in its database. We’ll do this with symetric encryption.

During the sign-up process, every user will get a symetric key which will be encrypted with the public key of the user and then stored on the server. 

During the login process, this key, encrypted with the public key of the user, will be sent to the client. It’ll be decrypted with the private key of the user, and then used for encryption / decryption of the data stored in the local database.

If your laptops gets stolen, all your data are encrypted with a key that is stored on the server! If the server gets hacked, there will be no messages on the server and only the public key of the user.


> Written with [StackEdit](https://stackedit.io/).