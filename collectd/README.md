# kafka-monitoring  collectd influxdb grafana

# Requirements
#### Java-8
sudo yum install -y java-1.8.0-openjdk
rm /usr/lib64/libjvm.so
ln -svf /usr/lib/jvm/jre/lib/amd64/server/libjvm.so /usr/lib64/libjvm.so
#### Disable selinux
vi /etc/sysconfig/selinux 


# Collectd instalation
yum install collectd collectd-generic-jmx collectd-java
#### enable collect service
systemctl enable collectd.service

#### kafka with collectd

 - add **kafka.conf** and **network.conf**  to **/etc/collectd.d/**
  
 - replace *{influxdb.hostname}* in **network.conf** by the influxdb   
  
 - hostname replace *{hostname}* and *{jmx_port}* in **kafka.conf** by  
   dedicated values

#### restart collectd service 
service collectd restart
#### check collectd service 
service collectd status -l

# Influxdb 

#### influxdb instalation 

    cat <<EOF | sudo tee /etc/yum.repos.d/influxdb.repo
    [influxdb]
    name = InfluxDB Repository - RHEL \$releasever
    baseurl = https://repos.influxdata.com/rhel/\$releasever/\$basearch/stable
    enabled = 1
    gpgcheck = 1
    gpgkey = https://repos.influxdata.com/influxdb.key
    EOF

    sudo yum install influxdb
    systemctl enable influxdb.service
    sudo service influxdb start

#### enable  [[collectd]] listener
vi /etc/influxdb/influxdb.conf

     [[collectd]]
      enabled = true
      bind-address = ":25826" # the bind address
      database = "kafka_collectd_db" # Name of the database that will be written to
      retention-policy = ""
      batch-size = 5000 # will flush if this many points get buffered
      batch-pending = 10 # number of batches that may be pending in memory
      batch-timeout = "10s"
      read-buffer = 0 # UDP read buffer size, 0 means to use OS default
      typesdb = "/usr/share/collectd/types.db"

  
  #### Create kafka monitoring database

    $ influx
    CREATE DATABASE kafka_collectd_db
    CREATE USER root WITH PASSWORD 'toor' WITH ALL PRIVILEGES
    CREATE USER monitor WITH PASSWORD 'monitoring'
    CREATE USER grafana WITH PASSWORD 'grafana'
    GRANT ALL ON kafka_collectd_db TO monitor
    GRANT READ ON kafka_collectd_db TO grafana
    exit

# Grafana

load **kafka.dashboard.json** to grafana to start monitoring

> For garfana installation see the grafana part of this project [/grafana]