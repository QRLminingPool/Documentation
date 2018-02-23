---
layout: page
title: Setup Environment
permalink: /environment/
---

#### INSTALL OF LATEST CMAKE

* Uninstall the default version provided by Ubuntu's package manager:

   `sudo apt-get purge cmake`   

* Go to the official [CMake website](https://cmake.org/download/) and download,
  extract the latest version (v.3.10.2 at time of writing). Update the version and build variables using the following commands to get the desired veriion. 


   `version=3.10`      
   `build=2`      
   `mkdir ~/temp`      
   `cd ~/temp`      
   `wget https://cmake.org/files/v$version/cmake-$version.$build.tar.gz`      
   `tar -xzvf cmake-$version.$build.tar.gz`      
   `cd cmake-$version.$build/`   

* Install the extracted source by running:

   `./bootstrap`      
   `make -j4`      
   `sudo make install`      

* Test your new cmake version.

   `$ cmake --version`      

   `Results of cmake --version:`      

   `cmake version 3.10.X`      

-----------------------------------
#### INSTALL NVM AND NODEJS

* The better way to install the latest NodeJS and keep various versions of Node speparate is to use the NVM (Node Virtual Environment)

   `sudo apt-get update`      
   `sudo apt-get install build-essential libssl-dev`      

   `curl -sL https://raw.githubusercontent.com/creationix/nvm/v0.31.0/install.sh -o install_nvm.sh`      

   `bash install_nvm.sh`         

   `source ~/.profile`      

   `nvm ls-remote`      

   `nvm use [VERSION.0.0]`      

   `nvm alias default [VERSION.0.0]`      

   `nvm use default`   

#### INSTALL REDIS

* Taken from the [Redis Quickstart](https://redis.io/topics/quickstart)

   `wget http://download.redis.io/redis-stable.tar.gz`      
   `tar xvzf redis-stable.tar.gz`      
   `cd redis-stable`      
   `make`      

   `redis-server`   

#### TO FIX REDIS-SERVER THP WARNINGS

* From this [Stackoverflow question:](https://stackoverflow.com/questions/40974024/what-is-the-correct-way-to-resolve-warnings-at-redis-startup-when-using-docker) Note must be root. (Use `su`.   )

   `[root@769368ed0fc5 /]# sysctl -w net.core.somaxconn=65535`      
   `[root@769368ed0fc5 /]# echo 'vm.overcommit_memory = 1' >> /etc/sysctl.conf`    
   `[root@769368ed0fc5 /]# sysctl vm.overcommit_memory=1`         
   `[root@769368ed0fc5 /]# echo never > /sys/kernel/mm/transparent_hugepage/enabled`      
   `[root@769368ed0fc5 /]# echo never > /sys/kernel/mm/transparent_hugepage/enabled`   


#### SECURING REDIS

By default Redis binds to all the interfaces and has no authentication at all. If you use Redis into a very controlled environment, separated from the external internet and in general from attackers, that's fine. However if Redis without any hardening is exposed to the internet, it is a big security concern. If you are not 100% sure your environment is secured properly, please check the following steps in order to make Redis more secure, which are enlisted in order of increased security.

>Make sure the port Redis uses to listen for connections (by default 6379 and additionally 16379 if you run Redis in cluster mode, plus 26379 for Sentinel) is firewalled, so that it is not possible to contact Redis from the outside world.
Use a configuration file where the bind directive is set in order to guarantee that Redis listens just in as little network interfaces you are using. For example only the loopback interface (127.0.0.1) if you are accessing Redis just locally from the same computer, and so forth.
Use the requirepass option in order to add an additional layer of security so that clients will require to authenticate using the AUTH command.
Use spiped or another SSL tunnelling software in order to encrypt traffic between Redis servers and Redis clients if your environment requires encryption.

Note that a Redis exposed to the internet without any security is very simple to exploit, so make sure you understand the above and apply at least a firewalling layer. After the firewalling is in place, try to connect with redis-cli from an external host in order to prove yourself the instance is actually not reachable. 

INSTALLING REDIS MORE PROPERLY

Running Redis from the command line is fine just to hack a bit with it or for development. However at some point you'll have some actual application to run on a real server. For this kind of usage you have two different choices:

>Run Redis using screen.
Install Redis in your Linux box in a proper way using an init script, so that after a restart everything will start again properly.

A proper install using an init script is strongly suggested. The following instructions can be used to perform a proper installation using the init script shipped with Redis 2.4 in a Debian or Ubuntu based distribution.

We assume you already copied redis-server and redis-cli executables under /usr/local/bin.

>Create a directory where to store your Redis config files and your data:>>

`sudo mkdir /etc/redis`
`sudo mkdir /var/redis`

>Copy the init script that you'll find in the Redis distribution under the utils directory into /etc/init.d. We suggest calling it with the name of the port where you are running this instance of Redis. For example:

`sudo cp utils/redis_init_script /etc/init.d/redis_6379`   

`Edit the init script.`   

`sudo nano /etc/init.d/redis_6379`   

Make sure to modify REDISPORT accordingly to the port you are using. Both the pid file path and the configuration file name depend on the port number.

>Copy the template configuration file you'll find in the root directory of the Redis distribution into /etc/redis/ using the port number as name, for instance:

`sudo cp redis.conf /etc/redis/6379.conf`   


>Create a directory inside /var/redis that will work as data and working directory for this Redis instance:

`sudo mkdir /var/redis/6379`   

>Set daemonize to yes (by default it is set to no).
Set the pidfile to /var/run/redis_6379.pid (modify the port if needed).
Change the port accordingly. In our example it is not needed as the default port is already 6379.

>Set your preferred loglevel.
Set the logfile to /var/log/redis_6379.log
Set the dir to /var/redis/6379 (very important step!)
Finally add the new Redis init script to all the default runlevels using the following command:

    sudo update-rc.d redis_6379 defaults

You are done! Now you can try running your instance with:

`sudo /etc/init.d/redis_6379 start`   

Make sure that everything is working as expected:

>Try pinging your instance with redis-cli.
Do a test save with redis-cli save and check that the dump file is correctly stored into /var/redis/6379/ (you should find a file called dump.rdb).
Check that your Redis instance is correctly logging in the log file.
If it's a new machine where you can try it without problems make sure that after a reboot everything is still working.

Note: In the above instructions we skipped many Redis configuration parameters that you would like to change, for instance in order to use AOF persistence instead of RDB persistence, or to setup replication, and so forth. Make sure to read the example redis.conf file (that is heavily commented) and the other documentation you can find in this web site for more information.     

  
         
