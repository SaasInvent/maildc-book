Redis
-----


[Redis,](https://www.digitalocean.com/community/tutorials/how-to-install-and-use-redis) developed in 2009, is a flexible, open-source, key value data store. Following in the footsteps of other NoSQL databases, such as Cassandra, CouchDB, and MongoDB, Redis allows the user to store vast amounts of data without the limits of a relational database. Additionally, it has also been compared to memcache and can be used, with its basic elements as a cache with persistence.

Setup
-----

Before you install redis, there are a couple of prerequisites that need to be downloaded to make the installation as easy as possible.

Start off by updating all of the apt-get packages:

    sudo apt-get update

Once the process finishes, download a compiler with build essential which will help us install Redis from source:

 

    sudo apt-get install build-essential

 
Finally, we need to download tcl:

 

    sudo apt-get install tcl8.5

Installing Redis
----------------

With all of the prerequisites and dependencies downloaded to the server, we can go ahead and begin to install redis from source:

Download the tarball from google code. The latest stable version is 2.8.9.

    wget http://download.redis.io/releases/redis-2.8.9.tar.gz

Untar it and switch into that directory:

    tar xzf redis-2.8.9.tar.gz
    cd redis-2.8.9


Proceed to with the make command:

    make

Run the recommended make test:

    make test

Finish up by running make install, which installs the program system-wide.

    sudo make install

Once the program has been installed, Redis comes with a built in script that sets up Redis to run as a background daemon.

To access the script move into the utils directory:

cd utils
From there, run the Ubuntu/Debian install script:

sudo ./install_server.sh
As the script runs, you can choose the default options by pressing enter. Once the script completes, the redis-server will be running in the background.

You can start and stop redis with these commands (the number depends on the port you set during the installation. 6379 is the default port setting):

sudo service redis_6379 start
sudo service redis_6379 stop
You can then access the redis database by typing the following command:

redis-cli
You now have Redis installed and running. The prompt will look like this:

redis 127.0.0.1:6379> 
To set Redis to automatically start at boot, run:

sudo update-rc.d redis_6379 defaults


> Written with [StackEdit](https://stackedit.io/).