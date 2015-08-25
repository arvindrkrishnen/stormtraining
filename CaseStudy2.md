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

### Build and Deploy Storm Topology, but Ensure  STORM is in path

export PATH=$PATH:/usr/local/storm/bin

###Open Project in IntelliJ - IntelliJ_Workplace from the path - /home/osboxes/Documents/IntelliJ_Workplace
### Access the file - harness.java and make changes as appropriate in the java file

### Create JAR file in IntelliJ by clicking on Modules -> Create JAR -> specify location as given below (default available in the instance)
### Manually delete .SF and .RSA files from Manifest folder within JAR file before run the topology
### Manually delete defaults.yaml in the JAR file

## Run the Storm topology - Local  cluster from the terminal in the project folder path

storm jar /home/osboxes/Documents/IntelliJ_Workplace/out/artifacts/IntelliJ_Workplace_jar/IntelliJ_Workplace.jar MyStormClassTopology

### Check the topology status in Storm UI through browser : localhost:8080/index.html
