Introduction

SIMCOM SIM300 module

 - the internal SIMCOM module (DCE
 - the external World (DTE)

AT Commands
----------

AT+CGMI : modem information

[Our modem is based on Quectel M10](http://www.quectel.com/UploadFile/Product/Quectel_M10_GSM_Specification_V3.0.pdf)

AT+CSQ : signal strength

AT+CREG? : registration on the network

AT+CREG?	What is GPRS attach status?	
AT+CREG:0,0 -> Trying to attach.
AT+CREG:0,1 -> Attached.
AT+CREG:0,2 -> Failed and stopped.
AT+CREG:0,3 -> Banned Networks.
AT+CREG:0,5 -> Logged in and roaming.


AT+CGMR	What is firmware version?



AT+CMGF=	OK	Specifies the input and output format of the short messages. 0 for PDU mode and 1 for text mode.
AT+CMGS	 	Sends a message.
AT+CMGR=*	 	Reads a message. * is the number of the message.



SETTING UP YOUR SYSTEM
1. This setup assumes that you have installed the usbserial driver distributed on your
Linux CD according to the instructions in the driver installation readme file. Ubuntu
has this setup by default.
2. Open an xterm window unload the usbserial driver by entering (you will be required
to enter the root password after entering this command):
“sudo modprobe –r usbserial”
After unloading the USB driver you should be returned to the command prompt.
3. On the terminal, load the driver to match your card Vendor ID and product ID.
Please lookup the proper product ID in Table 1.
“sudo modprobe usbserial vendor=[vendorID] product=[prodID]”
Where prodID is the proper product ID that corresponds to your modem card in
Table 1. Although it is not necessary to verify the result of running the command
above, but if you want to be sure that the driver is now associated with your card.
Plug in your card to the computer and make sure you have power then run the
following on the command-line:
“sudo dmesg|grep –i ttyUSB”
You should see something like:
usb 5-1: generic converter now attached to ttyUSB0



Network registration information (CREG) output depends on modem configuration.

By sending "AT+CREG=0" one can disable network registration code (which is your case)
By sending "AT+CREG=1" one can enable network registration
By sending "AT+CREG=2" one can enable network registration code with
location information (location area code and cell ID)
Options 2. and 3. will also automatically emit +CREG messages upon modem boot/network change.


ps: one should not forget to save current AT configuration by executing AT&W

Disable pin code
----------------

This article explains [how to disable pin code](http://dostmuhammad.com/blog/disable-pin-code-using-gsm-modem-at-commands/) :
If you have a PIN code enabled SIM card and want to remove /disable PIN code using AT commands follow these commands,
suppose 9546 is the current PIN code , Replace 9546 with your PIN code, >>> shows the response from modem.


    AT+CPIN?
    +CPIN: SIM PIN // pin codes need to be entered
    OK
     
    AT+CPIN="1234"
    OK
     
    AT+CLCK="SC",0,"1234" // disable pin code
    OK
     
    AT+CPIN? 
    +CPIN: READY

Another [interesting article](http://dostmuhammad.com/blog/gsm-modemmodule-not-responding-to-at-commands-after-firmware-upgrade/) from the same source.



T+CMGF – Set SMS Text Mode or SMS PDU Mode
The AT+CMGF command sets the GSM modem in SMS Text Mode or SMS PDU Mode.

In Text Mode, SMS messages are represented as readable text. In PDU Mode, all SMS messages are represented as binary strings encoded in hexadecimal characters like 31020B911326880736F40000A900.

Although Text Mode is easier to use, PDU Mode is more consistent on different GSM Modems.

Command	Positive Response
AT+CMGF=<mode><CR>	OK
Parameters
<mode>: 0 = PDU Mode, 1 = Text Mode
<CR> = ASCII character 13

Example
Set the GSM modem to Text Mode SMS and send a message to GSM number +31628870634.

AT+CMGF=1
OK
AT+CMGS="+31628870634"
> This is the text message.→
+CMGS: 198
OK


stty -F /dev/ttyUSB0 115200