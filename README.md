Telemetry for Cisco
=========

Introduction
===
The repo helps for the deployment of a monitoring system including telegraf, influxdb and chronograf.
This has been thought for telemetry with Cisco devices and tested with Cisco NX-OS devices. If you would like to use it with a different
type of devices, or different protocols such as SNMP, just configure the appropriate telegraf plugin (check configure telegraf part).

Install
===
Have a look at docker-compose.yml file, edit the ports or the volumes mappings if needed. 

Launch the containers :
```
docker-compose up -d
```

Configure database
===
Connect into the influxdb container, and create admin user, database and database user. This has to be done only once, as repository
is mapped on the host.

```
docker exec -it monitoring_influxdb_1 /bin/bash

root@b34fa9a249d4:/# influx
Connected to http://localhost:8086 version 1.7.7
InfluxDB shell version: 1.7.7
> CREATE USER admin WITH PASSWORD 'PASSWORD' WITH ALL PRIVILEGES
> auth
username: admin
password: 
> CREATE DATABASE DATABASE_NAME
> CREATE USER USERNAME WITH PASSWORD 'PASSWORD'
> GRANT ALL ON "USERNAME" TO "PASSWORD"
```

Configure telegraf
===
Edit telegraf.conf with the following lines.
Please note the input plugin is specific for telemetry, use a different input plugin if needed. The output plugin should be generic.
```
[[outputs.influxdb]]
  urls = ["http://influxdb:8086"]
  database = "DATABASE"
  username = "USERNAME"
  password = "PASSWORD"

[[inputs.cisco_telemetry_mdt]]
  transport = "grpc"
  service_address = ":57000"
```

Configure chronograf
===
Connect on chronograf web interface, go to configuration and configure the admin credentials so chronograf can authenticate to influxdb.
Configure the dashboard and enjoy!
