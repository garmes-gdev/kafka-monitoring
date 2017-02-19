# Garafana

### create user and group grafana 

    groupadd grafana
    useradd -g grafana grafana

### download grafana

    wget http://grafanarel.s3.amazonaws.com/builds/grafana-4.0.1-1480694114.linux-x64.tar.gz

### configuration

    tar xzvf grafana-4.0.1-1480694114.linux-x64.tar.gz
    mv grafana-4.0.1-1480694114  /opt/grafana
    
    chown grafana:grafana -R /opt/grafana
    chmod 744 -R /opt/grafana

### start grafana service

    export GRAFANA_USER="grafana"
    export GRAFANA_HOME="/opt/grafana"
    export GRAFANA_LOG_DIR="$GRAFANA_HOME/data/log"
    rm -rf $GRAFANA_HOME/data/sessions
    /sbin/runuser $GRAFANA_USER -c "$GRAFANA_HOME/bin/grafana-server &"

### stop garfana service 

    ps -ef | grep grafana | grep -v grep | awk "{print \$2}" | xargs -r kill



    http://{grafana.hostname}:3000
    
    login: admin
    pwd  : admin 
