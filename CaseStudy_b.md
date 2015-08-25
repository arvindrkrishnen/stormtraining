##Execute the steps one by one, to make sure you are starting all the services correctly.

## "starting the necessary servers"

source ~/.bashrc

echo "Starting Zookeeper"

export ZOO_LOG_DIR=/home/osboxes/Downloads/zookeeper/logs

sudo  /home/osboxes/Downloads/zookeeper/bin/zkServer.sh start

echo "Zookeeper Configuration Mode:"

echo stat | nc localhost 2181 | grep Mode

## Starting Storm

sudo service supervisor start

###supervisor configurations have already been updated to point to Storm configuration and bin directories.
### Verify Storm Status

sudo supervisorctl status

##Start Hadoop Service

/home/osboxes/Downloads/hadoop-2.6.0/sbin/start-dfs.sh

/home/osboxes/Downloads/hadoop-2.6.0/sbin/start-yarn.sh

##Start Solr

java -jar /opt/solr/latest/training/start.jar -Djetty.home=/opt/solr/latest/training -Dsolr.solr.home=/opt/solr/latest/training/solr

sleep 20

### Creating tweets core in Solr

curl "http://localhost:8983/solr/admin/cores?action=CREATE&name=tweets&instanceDir=/opt/solr/latest/training/solr/tweets"

###if the core-tweet is already loaded, then go to browser -> localhost:8983 -> core admin and unload the core tweet and try the above script again
###if you get connection refused, then SOLR did not get loaded properly. Check the service and process ID which is running on 8983 port number and kill it. Alternatively, check if HDFS is running properly

##Start Kafka

### Before starting Kafka, make sure  kafka-server-start.sh is updated as
### export KAFKA_HEAP_OPTS="-Xmx256M -Xms128M"

/opt/kafka/latest/bin/kafka-server-start.sh /opt/kafka/latest/config/server.properties 

sleep 10

## Create Topic for Tweets in Kafka

###Before running Kafka, make sure you have access to create log directory

cd /opt/kafka/latest

sudo chown -R osboxes:osboxes .

/opt/kafka/latest/bin/kafka-topics.sh --create --zookeeper localhost:2181 --replication-factor 1 --partitions 1 --topic tweets

### Build and Deploy Storm Topology, but Ensure  STORM is in path

export PATH=$PATH:/usr/local/storm/bin

###Open Project in IntelliJ - BigDataTraining-storm-twitter from the path - /home/osboxes/Documents/stormdemo/BigDataTraining-storm-twitter
### Access the file - harness.java and make changes as appropriate in the java file

###Download the below file and update in the location - storm/src/main/resources/storm.properties 
https://github.com/arvindrkrishnen/stormtraining/blob/master/storm.properties.caseStudy1

###Download the below file and update in the location - common/src/main/resources/kafka.properties
https://github.com/arvindrkrishnen/stormtraining/blob/master/kafka.properties.caseStudy1

###Download the below file and update in location /usr/local/storm/conf/storm.yaml
https://github.com/arvindrkrishnen/stormtraining/blob/master/storm.yaml 


### Create JAR file in IntelliJ by clicking on Modules -> Create JAR -> specify location as given below (default available in the instance)
### Manually delete .SF and .RSA files from Manifest folder within JAR file before run the topology

## Run the Storm topology - Remote cluster from the terminal in the project folder path

mvn -q clean install -pl storm -am

storm jar /home/osboxes/Documents/stormdemo/BigDataTraining-storm-twitter/classes/artifacts/BigDataTraining_storm_twitter_jar/BigDataTraining-storm-twitter.jar dashboard.storm.Harness

### Check the topology status in Storm UI through browser : localhost:8080/index.html
