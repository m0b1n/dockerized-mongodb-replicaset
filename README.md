# dockerized-mongodb-replicaset

![Image of Yaktocat](https://webassets.mongodb.com/_com_assets/cms/MongoDB_Logo_FullColorBlack_RGB-4td3yuxzjs.png)
# How To Configure Mongo Replica Set:

### First: install mongo on your machine

> we use mongo version 4.4

mongo website link : [mongodb site](mongodb.com)

* follow steps below:
1.  ``` wget -qO - https://www.mongodb.org/static/pgp/server-4.4.asc | sudo apt-key add - ```
1.  ``` sudo apt-get update ```
1.  ``` sudo apt-get install -y mongodb-org ```

Congratulaions, you've successfully installed mongo database
you can check your database status by

* ```  sudo systemctl status mongod.service ```

### Second: Config your database

there is two way you can setup a replica set mongo database

> First: we assume that this is our primary database server

> You should stop your databse by commads below:

* ``` sudo systemctl stop mongod.service ```
* ``` sudo systemctl disable mongod.service ```

>Then start an instance of mongo by:

* ``` mongod --replSet rs0 --logpath "file.log" --dbpath rs2 --port 27017 --bind_ip 0.0.0.0 & ```

the command will write mongo logs to **file.log**, 

also it will write database files to **rs2** folder. 

the port will set to 27017. 

remember the **--bind_ip** flag is important as it will set mongo on machine ip.

then the database is running. now we should config replica set on db.

follow the commands below

* ``` mongo ``` 
* ``` config = { _id: "rs0", members:[  {_id:0, host:"host_ip_address:27017"}]} ```
* ``` rs.initiate(config) ```

> congrats. your database is now a replica set ready

> Second: we can populate <ins>/etc/mongod.conf</ins>

> for this we have to enable mongod by systemctl command

> the file is something like this

```
# mongod.conf

# for documentation of all options, see:
#   http://docs.mongodb.org/manual/reference/configuration-options/

# Where and how to store data.
storage:
  dbPath: /var/lib/mongodb
  journal:
    enabled: true
#  engine:
#  mmapv1:
#  wiredTiger:

# where to write logging data.
systemLog:
  destination: file
  logAppend: true
  path: /var/log/mongodb/mongod.log

# network interfaces
net:
  port: 27017
  bindIp: 0.0.0.0


# how the process runs
processManagement:
  timeZoneInfo: /usr/share/zoneinfo

#security:
	#authorization: enabled
	#keyFile: /srv/mongodb/keyfile

#operationProfiling:

replication:
	replSetName: rs0

#sharding:
	#configDB: csRS/10.8.0.12:27001

## Enterprise-Only Options:

#auditLog:

#snmp:


```

### Third: Add another replica instace

> on another machine install mongodb.
> then start mongo with replica set initiated with the main db "like rs0"
> thats enough for this step

> go to main database and add this replica with command below:

``` rs.add("slave_host_ip_address:port") ```

> useful links:

* [youtube link](https://www.youtube.com/watch?v=3wus5trgi0A&t=191s)


# Bonus
> how to save a deamon from crashing

> go to service config file in

``` /lib/systemd/system/mongod.service ```

> add following lines to the end of service region

``` 
Restart=on-failure
RestartSec=5s
```

> then run 

``` sudo systemctl daemon-reload ```

> and start the service

> sleep well at nights!
