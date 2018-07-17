# Setting-up 

Before Beginning, make sure that [java is installed](https://www.digitalocean.com/community/tutorials/how-to-install-java-with-apt-get-on-ubuntu-16-04) in your system.

## Single Node Setup
Steps to be followed :
### Step 1 : Download and Install
* Go-to the folder you want to install this set-up into and download the respective `tar` file for setup.
* We will be using `/home/ms/installed` directory for setup.
```
$ cd /home/ms/installed
```
```
$ wget https://orientdb.com/download.php?file=orientdb-community-2.2.22.tar.gz
```

Change version as per your requirement.

* Extract the file
```
$ sudo tar -xf download.php?file=orientdb-community-2.2.22.tar.gz
```

* Move your content from `orientdb-community-2.2.22` to `orientdb` (just ethical concept)
```
 $ sudo mv orientdb-community-2.2.22 orientdb 
```

### Step 2 : Run Server for the first time : 
* Navigate to the installed directory 
``` 
$ cd orientdb 
```

* Then start the server
```
$ sudo bin/server.sh
```
* First time it will prompt you to enter `root` password. We will be using `user/password:root/root` as our authentication credential for orientdb.
  ![Start server first time](setup-orientdb-res/start_server.png)
* Open your web-browser and type `localhost:2480` to check whether server is started or not. 
* Orientdb uses `port:2424` for binary connections and `port:2480` for http connections.
* Type `ctrl+c` to close the running server.

### Step 3 : Connecting to the console
Connect to the running server using command line interface.

* Keep server running in one terminal window. Open another terminal window and run `console.sh`:
```
sudo /home/ms/installed/orientdb/bin/console.sh
```
* Now we need to connect to the server instance.
```
orientdb> connect remote:127.0.0.1 root root-password
```
Use password that you specified while setting up orientdb credentials. 
```
orientdb> connect remote:127.0.0.1 root root
```
* Type `exit` to quit whenever you want. 

### Step 4 : To install OrientDB as a service
#### Configuring OrientDB

At this point OrientDB is installed on your system, but it's just a bunch of scripts on the server. In this step, we'll modify the configuration file, and also configure it to run as a daemon on the system.

Start by modifying the `orientdb.sh` script to tell OrientDB the user it should be run as, and to point it to the installation directory.

* Create system user
```
$ sudo useradd -r orientdb -s /bin/false
```
* Give ownership of OrientDB directory to the user created
```
$ sudo chown -R orientdb:orientdb /home/ms/installed/orientdb
```
* Changes to be made in `orientdb.sh`. Open it using 
```
$ sudo nano /home/ms/installed/orientdb/bin/orientdb.sh
```
 At top of file there are these two lines : 
```
ORIENTDB_DIR="YOUR_ORIENTDB_INSTALLATION_PATH"
ORIENTDB_USER="USER_YOU_WANT_ORIENTDB_RUN_WITH" 
```
**Change** them to :
```
ORIENTDB_DIR="/home/ms/installed/orientdb"
ORIENTDB_USER="orientdb" 
```
* Now we need to make it possible for user to run script using `sudo` command.
Under `start` function, look for the line and comment it as follows : 
```
#su -c "cd \"$ORIENTDB_DIR/bin\"; /usr/bin/nohup ./server.sh 1>../log/orientdb.log 2>../log/orientdb.err &" - $ORIENTDB_USER 
```
Copy and Paste this line just under it : 
```
sudo -u $ORIENTDB_USER sh -c "cd \"$ORIENTDB_DIR/bin\"; /usr/bin/nohup ./server.sh 1>../log/orientdb.log 2>../log/orientdb.err &" 
```
Under `stop` function, comment out the line as follows :
```
#su -c "cd \"$ORIENTDB_DIR/bin\"; /usr/bin/nohup ./shutdown.sh 1>>../log/orientdb.log 2>>../log/orientdb.err &" - $ORIENTDB_USER 
```
Add this line just under it : 
``` 
sudo -u $ORIENTDB_USER sh -c "cd \"$ORIENTDB_DIR/bin\"; /usr/bin/nohup ./shutdown.sh 1>>../log/orientdb.log 2>>../log/orientdb.err &" 
```
Save and Close the file.


* There is no need to add any more user further. But, if you want to add another user, other than `root` and `guest`, open file `orientdb-server-config.xml`
```
$ sudo nano /home/ms/installed/orientdb/config/orientdb-server-config.xml 
```
Scroll to user tag and add :
` <user name="username" password="password" resources="*"/> `

Save and Close the file

#### Installing Startup Script
Now all we need to do is to make the system aware of the new script. The system will than automatically start it on every boot

Script are ready now and they needs to be copied into their repective directory .

* Copy script responsible for running the console to `/usr/bin` directory. 
```
$ sudo cp /home/ms/installed/orientdb/bin/console.sh /usr/bin/orientdb
```
* Copy script responsible for starting and stopping the service or daemon to `/etc/init.d` directory.
```
$ sudo cp /home/ms/installed/orientdb/bin/orientdb.sh /etc/init.d/orientdb
```
Change to `.etc.init.d` directory and update `rc.d` directory 
```
$ cd /etc/init.d
```
```
$ sudo update-rc.d orientdb defaults
```

### Step 5 : Starting OrientDB
Now you are all setup to start working with OrientDB.

* Direct start from the orientdb directory **(preferred method)**.
```
$ sudo /home/ms/installed/orientdb/bin/server.sh
```
* To start as a Service : 
```
$ sudo service orientdb start
```

## Distributed Server Set-up 
There are two ways to share a database across multiple server nodes:
* Prior to startup, copy the specific database directory, under $ORIENTDB_HOME/database to all servers.
* Keep the database on the first running server node, then start every other server node. Under the default configurations, OrientDB automatically shares the database with the new servers that join.
We will follow the second procedure.

### Step 1: Distributed Configuration
Distributed configuration requires rendering of three files:

|File Name|
|-|
|orientdb-server-config.xml|
|default-distributed-db-config.json|
|hazelcast.xml|

#### orientdb-server-config.xml
Add and enable the `OHazelcastPlugin` to enable clustering between nodes.
For our setup `${ORIENTDB_HOME} = /home/ms/installed/orientdb`
```
<handler class="com.orientechnologies.orient.server.hazelcast.OHazelcastPlugin">
  <parameters>
    <!-- NODE-NAME. IF NOT SET IS AUTO GENERATED THE FIRST TIME THE SERVER RUN -->
    <!-- <parameter name="nodeName" value="europe1" /> -->
    <parameter name="enabled" value="true" />
    <parameter name="configuration.db.default"
               value="${ORIENTDB_HOME}/config/default-distributed-db-config.json" />
    <parameter name="configuration.hazelcast"
               value="${ORIENTDB_HOME}/config/hazelcast.xml" />
  </parameters>
</handler>
```
for details... [click](http://orientdb.com/docs/2.0/orientdb.wiki/Distributed-Configuration.html#orientdb-server-configxml)

#### default-distributed-db-config.json
This JSON file contains default configuration for databases and it gets copied in the databases's folder when first time database is run in distributed version. Now, each time there is change in database, database specific file is changed.

for details... [click](http://orientdb.com/docs/2.0/orientdb.wiki/Distributed-Configuration.html#default-distributed-db-configjson)

#### hazelcast.xml
Two type of network configuration:

* `Multicast` : This method is used for automatically discovery over a LAN connection. You can change `multicast-group` to distinguish between different groups on the network. Default configuration
```
<hazelcast>
  ...
  <network>
    <port auto-increment="true">2434</port>
    <join>
      <multicast enabled="true">
        <multicast-group>235.1.1.1</multicast-group>
        <multicast-port>2434</multicast-port>
      </multicast>
     </join>
  </network>
  ...
</hazelcast>
```

* `Manual-IP`: Use TCP-IP tag in configuration if you want to assign Hostname/IP-addresses manually. Disable the multicast:
```
<hazelcast>
  ...
  <network>
    <port auto-increment="true">2434</port>
    <join>
      <multicast enabled="false">
        <multicast-group>235.1.1.1</multicast-group>
        <multicast-port>2434</multicast-port>
      </multicast>
      <tcp-ip enabled="true">
        <member>192.168.1.0-7:2434</member>
      </tcp-ip>
     </join>
  </network>
  ...
</hazelcast>
```
for details... [click](http://orientdb.com/docs/2.0/orientdb.wiki/Distributed-Configuration.html#hazelcastxml)


### Step 2: Starting the First Server Node
* Unlike the standard standalone deployment of OrientDB, there is a different script that you need to use when launching a distributed server instance. Instead of `server.sh`, you use `dserver.sh`. Whichever you need, you can find it in the bin of your installation directory.

 ```
 $ ./bin/dserver.sh
 ```

* OrientDB uses the same orientdb-server-config.xml configuration file, regardless of whether it's running as a server or distributed server.
* The first time you start OrientDB as a distributed server, it generates the following output:

```
 +---------------------------------------------------------------+
|         WARNING: FIRST DISTRIBUTED RUN CONFIGURATION          |
+---------------------------------------------------------------+
| This is the first time that the server is running as          |
| distributed. Please type the name you want to assign to the   |
| current server node.                                          |
|                                                               |
| To avoid this message set the environment variable or JVM     |
| setting ORIENTDB_NODE_NAME to the server node name to use.    |
+---------------------------------------------------------------+

Node name [BLANK=auto generate it]:
```

* Give the node a name here. OrientDB stores it in the nodeName parameter of OHazelcastPlugin. It adds the variable to your orientdb-server-config.xml configuration file.

### Step 3: Starting other nodes

* For the other server nodes in the cluster, use the same dserver.sh command as you used in starting the first node.
```$ ./bin/dserver.sh
```
* When the other server nodes come online, they begin to establish network connectivity with each other.
* Check by connecting to the database
```
$ connect remote:localhost/GratefulDeadConcerts admin admin
```
* Then create a class
```
$ create class person extends v
```
* Check this class is created in every node connected to the server.  

## Accessing a new database

While the default database `GratefulDead` is both accesible & reflect changes over the different systems , one needs to be careful while interacting with new database. 

Steps to be followed :

* Within the system which will host the database
  * Make sure you are connected to localhost
  ```
  connect remote:localhost user_name password
  ```
  The user_name,password mentioned above are the ones which we set while creating the orientDB server.

  * Creating the database

  ```
  create database remote:full/path/to/database database_user_name database_password databse_type
  ```
  The user_name,password mentioned above are database specific.

  Example

  ```
  create database remote:/home/ms/installed/orientdb/databases/mydatabase user pass plocal
  ```
* Within the system from which you want to connect to the database
```
connect remote:localhost/database_name database_user_name database_password
```

Example
```
connect remote:localhost/mydatabase user pass
```

# Troubleshooting

## Error while restarting the server

If the server is not properly shutdown , it may result to problems while restarting the server. For this, as a precaution, everytime you stop the server, within `bin` of orientdb  run the command
```
./shutdown.sh
```
The result message displayed will be
```
pid file detected, killing process...
waiting for OrientDB server to shutdown
```

# Refereces
* [Sample OrientDB Code](https://github.com/namikaze-nitin/orientdb-neurosys-poc)
* [Orientdb Code Documentation](code-orientdb-api.md)
* [Single Node Set up](https://www.digitalocean.com/community/tutorials/how-to-install-and-configure-orientdb-on-ubuntu-14-04)
* [Official website link for setup](http://orientdb.com/docs/2.0/orientdb.wiki/Tutorial-Installation.html)
* [Distributed Configuration](http://orientdb.com/docs/2.0/orientdb.wiki/Distributed-Configuration.html#hazelcastxml)