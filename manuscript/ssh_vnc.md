All you ever wanted to know about SSH

Here's a good list:

 - [document one](http://doc.ubuntu-fr.org/ssh#configuration_du_serveur_ssh)
 - [document two](http://kb.mediatemple.net/questions/1626/Using%20SSH%20keys%20on%20your%20server)
 - a [VNC server on Raspberry](http://www.everydaylinuxuser.com/2014/03/connect-to-raspberry-pi-from-hp.html)


Installing VNC Server on the Raspberry PI
-----------------------------------------

You should now have a connection to the Raspberry PI from the Chromebook using SSH. This is great for doing command line stuff but if you want to do things in a graphical environment you will need a few more tools installed.

The first thing that needs to be installed is VNC Server.  Make sure you are connected using SSH.

Type the following to install the VNC Server:

    sudo -i
    apt-get install tightvncserver

You will be asked if you are sure you want to continue. Press "Y" to continue.

The VNC Server will be installed.
---------------------------------

At this point I would advise writing a script that you can run whenever you want to as typing the whole command that you need to run the VNC server is something you only really want to do once.

Type the following to open the nano editor:

    cd /etc/init.d
    vi vncserver

Enter the following script into the editor:

    #!/bin/sh -e
    export USER="pi"
    
    DISPLAY="1"
    DEPTH="16"
    GEOMETRY="1200x720"
    NAME="VNCserver"
    OPTIONS="-name ${NAME} -depth ${DEPTH} -geometry ${GEOMETRY} :${DISPLAY}"
    . /lib/lsb/init-functions
    case "$1" in
    start)
    log_action_begin_msg "Starting vncserver for user '${USER}' on localhost:{$DISPLAY}"
    su ${USER} -c "/usr/bin/vncserver ${OPTIONS}"
    ;;
    stop)
    log_action_begin_msg "Stopping vncserver for user '${USER}' on localhost:{$DISPLAY}"
    su ${USER} -c "/usr/bin/vncserver -kill :${DISPLAY}"
    ;;
    restart)
    $0 stop
    $0 start
    ;;
    esac
    exit 0

Press CTRL and O to save the script and CTRL and X to exit nano.

I don't advise copying and pasting scripts into your computer without knowing what they are doing so I will do my best to explain.

The VNCServer takes a number of parameters. In the script above we use three of them. "DISPLAY" is used to determine which port number to use. VNCServer by default runs on port 5900. By setting display to 1 you are specifying that VNCServer will run on port 5901. The DEPTH variable specifies the colour depth. Finally the GEOMETRY option specifies the screen resolution to use.

By specifying these options early in the script it makes it easy to adjust them to suit your needs.

The OPTIONS line takes all of the parameters and appends them as input parameters for the VNCServer command.

There is now a case statement. The case statement will look at the argument provided when you run the script and will run the appropriate code dependent upon the argument you specified. For example if you type vncserver start the code after start) will run. If you run vncserver stop the code after stop) will run. I think you get the idea now.

When you specify the start option a message is sent to the terminal stating that the VNC server is starting. The user is then switched to the user account specified by the export USER command and the VNC Server is started with the options specified as that user.

The stop option kills the server. The restart option, stops and restarts the VNC Server.

In order to be able to run the script at all you need to make it executable. To do this type the following:

    chmod +x vncserver

To make sure you haven't made any mistakes enter the following:

    ./vncserver start

If any errors appear then the script hasn't been entered correctly. Make sure the script matches the script above and try again.

It is likely that as this is your first time running VNC server that you will be asked to enter a password for connecting to the PI. You will also be asked if you want to create a readonly password. It is up to you if you want to do this.

From chrome book

    192.168.1.101:5901


Running the VNC Server at startup
---------------------------------

The VNC server is currently running but as soon as you shut down the Raspberry PI that is it. The next time you start the Raspberry PI you won't be able to VNC to it.

There are two things you can do. The first is to SSH onto the Raspberry PI and run the startup script again. The second is to set the VNC Server to start when the Raspberry PI starts.

My preferred method is to use SSH. Simply connect via SSH to the Raspberry PI and then enter the following command when you want to VNC onto the Raspberry PI:

    cd /etc/init.d
    ./vncserver start

I think this is the best way as it only leaves VNC open for connection when you actually plan to use it as opposed to leaving it open all the time.

If however you want to always be able to connect via VNC you can run the following command into the terminal just once and the VNC server will start when the Raspberry PI starts:

    update-rc.d vncserver defaults


Get your IP address sent to your email account
----------------------------------------------

As I mentioned earlier one of the problems you will encounter is that your Raspberry PI's IP address can and will change when you restart it.

You can use trial and error to find the Raspberry PI if you wish by trying 192.168.1.101 and then 192.168.1.102, 192.168.1.103 and so on until you get connected.

The next bit is entirely optional but will help you if you need your PI's IP address.

First things first you will need a piece of software called SSMTP installed. Connect to the Raspberry PI via SSH and enter the following:

    sudo -i
    apt-get install ssmtp

The conf file needs to be edited to be able to set up the outgoing email settings. Type the following:

    cd /etc/ssmtp
    vi ssmtp.conf

Add the following lines to the end of the file:

    root=postmaster 
    mailhub=smtp.gmail.com:587
    UseSTARTTLS=YES
    FromLineOverride=YES
    AuthUser=login_gmail
    AuthPass=password_gmail

*Enter user and password without quotes!*
Now you will need to edit the file /etc/ssmtp/revaliases. To do this type the following:

    cd /etc/ssmptp
    vi revaliases

Add the following at the end of the file:

    root:your_gmail_address:smtp.gmail.com:587
    pi:your_gmail_address:smtp.gmail.com:587
  


Change the permissions on the ssmtp.conf file to enable emails to be sent:

    cd /etc/ssmptp
    chmod 774 ssmtp.conf

In order to be able to install emails install the following packet:

    apt-get install mailutils

Now create a file called sendmyIP.sh by typing the following:

    vi  /etc/profile.d/sendmyip.sh

Enter the following into the editor:

    ifconfig | mail -s "Your PI IP " your_email_address


Change the permissions to enable the script to run by typing the following:

    cd /etc/profile.d
    chmod +x sendmyip.sh

 
Now every time your PI starts it should send you an email with the internal IP address for the PI.

Another option is to set the IP address for the Raspberry PI as static on dhcp server : yes there is something like a static dhcp address!!


Get your External IP address
----------------------------

When you connect to the internet you will actually be using an external IP address. 

I followed [this guide](http://www.if-not-true-then-false.com/2010/linux-get-ip-address/) to get an external IP address.

I installed lynx first by running the following in the command line:

    apt-get install lynx

Then I ran the following command to get the external IP address.

    lynx --dump http://ipecho.net/plain

