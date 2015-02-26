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

    apt-get install tightvncserver

You will be asked if you are sure you want to continue. Press "Y" to continue.

The VNC Server will be installed.
---------------------------------

At this point I would advise writing a script that you can run whenever you want to as typing the whole command that you need to run the VNC server is something you only really want to do once.

Type the following to open the nano editor:

    vi /etc/init.d/vncserver

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

sudo chmod +x /etc/init.d/vncserver

To make sure you haven't made any mistakes enter the following:

sudo /etc/init.d/vncserver start

If any errors appear then the script hasn't been entered correctly. Make sure the script matches the script above and try again.

It is likely that as this is your first time running VNC server that you will be asked to enter a password for connecting to the PI. You will also be asked if you want to create a readonly password. It is up to you if you want to do this.