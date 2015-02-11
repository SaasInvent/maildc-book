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







> Written with [StackEdit](https://stackedit.io/).