# Hadoop Notes
## Contents
* [Environment Setup](#environment-setup)
  * [Local Standalone Setup](#local-standalone-environment-setup)
  * [Pseudo-distributed Single Node Setup](#pseudo-distributed-single-node-environment-setup)
* [HDFS](#hdfs)
* [MapReduce](#mapreduce)
* [Troubleshooting](#troubleshooting)
* [References](#references)

## Environment Setup
### Local Standalone Environment Setup
1. Install Java
```
sudo apt install default-jdk default-jre
```
2. Install OpenSSH server and client
```
sudo apt install openssh-server openssh-client
```
3. Add new user
```
sudo adduser hadoop
```
4. Switch to new user
```
sudo su - hadoop
```
5. Generate SSH public and private keys
```
ssh-keygen -t rsa
```
6. Add SSH public key to authorized keys
```
cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys
```
7. Change permissions of authorized keys
```
chmod 640 ~/.ssh/authorized_keys
```
8. Verify SSH configuration
```
ssh localhost
```
9. Get [latest version of Hadoop](https://downloads.apache.org/hadoop/common/stable/)
```
wget https://downloads.apache.org/hadoop/common/hadoop-3.3.6/hadoop-3.3.6.tar.gz
```
10. Extract tar file
```
tar -xvzf hadoop-3.3.6.tar.gz
```
11. Rename directory
```
mv hadoop-3.3.6 hadoop
```
12. Obtain OpenJDK directory
```
dirname $(dirname $(readlink -f $(which java)))
```
13. Add environment variables to bash configuration `~/.bashrc`
```
export JAVA_HOME=/usr/lib/jvm/java-11-openjdk-amd64
export HADOOP_HOME=/home/hadoop/hadoop
```
14. Reload bash settings
```
source ~/.bashrc
```
15. Confirm setup by using example MapReduce Java archive file to obtain word counts
```
mkdir input
cp $HADOOP_HOME/*.txt input
hadoop jar $HADOOP_HOME/share/hadoop/mapreduce/hadoop-mapreduce-examples-3.3.6.jar  wordcount input output
cat output/*
```

### Pseudo-distributed Single Node Environment Setup
1. Set up local environment as above
2. Add environment variables to bash configuration: `~/.bashrc`
```
export JAVA_HOME=/usr/lib/jvm/java-11-openjdk-amd64
export HADOOP_HOME=/home/hadoop/hadoop
export HADOOP_INSTALL=$HADOOP_HOME
export HADOOP_MAPRED_HOME=$HADOOP_HOME
export HADOOP_COMMON_HOME=$HADOOP_HOME
export HADOOP_HDFS_HOME=$HADOOP_HOME
export HADOOP_YARN_HOME=$HADOOP_HOME
export HADOOP_COMMON_LIB_NATIVE_DIR=$HADOOP_HOME/lib/native
export PATH=$PATH:$HADOOP_HOME/sbin:$HADOOP_HOME/bin
export HADOOP_OPTS="-Djava.library.path=$HADOOP_HOME/lib/native"
```
3. Reload bash settings
```
source ~/.bashrc
```
4. Create node metadata directories
```
mkdir -p /home/hadoop/hadoop/hdfs/{namenode,datanode}
```
5. Change to Hadoop configuration directory
```
cd hadoop/etc/hadoop
```
6. Add Java environment variables to `hadoop-env.sh` file
```
export JAVA_HOME=/usr/lib/jvm/java-11-openjdk-amd64
```
7. Edit configuration in `core-site.xml`
```
<configuration>
  <property>
    <name>fs.defaultFS</name>
    <value>hdfs://localhost:9000</value>
  </property>
</configuration>
```
8. Edit configuration in `hdfs-site.xml`
```
<configuration>
  <property>
    <name>dfs.replication</name>
    <value>1</value>
  </property>
  <property>
    <name>dfs.name.dir</name>
    <value>file:///home/hadoop/hadoop/hdfs/namenode</value>
  </property>
  <property>
    <name>dfs.data.dir</name>
    <value>file:///home/hadoop/hadoop/hdfs/datanode</value>
  </property>
</configuration>
```
9. Edit configuration in `mapred-site.xml`
```
<configuration> 
  <property> 
    <name>mapreduce.framework.name</name> 
    <value>yarn</value> 
  </property>
  <property>
    <name>mapreduce.application.classpath</name>
    <value>$HADOOP_MAPRED_HOME/share/hadoop/mapreduce/*:$HADOOP_MAPRED_HOME/share/hadoop/mapreduce/lib/*</value>
  </property>
</configuration>
```
10. Edit configuration in `yarn-site.xml`
```
<configuration>
  <property>
    <name>yarn.nodemanager.aux-services</name>
    <value>mapreduce_shuffle</value>
  </property>
  <property>
    <name>yarn.nodemanager.env-whitelist</name>
    <value>JAVA_HOME,HADOOP_COMMON_HOME,HADOOP_HDFS_HOME,HADOOP_CONF_DIR,CLASSPATH_PREPEND_DISTCACHE,HADOOP_YARN_HOME,HADOOP_HOME,PATH,LANG,TZ,HADOOP_MAPRED_HOME</value>
  </property>
</configuration>
```
## HDFS
1. Switch to Hadoop user account
```
sudo su - hadoop
```
2. Remove previous nodes and format HDFS name node
```
cd ~
rm -R ~/hadoop/hdfs/namenode/*
rm -R ~/hadoop/hdfs/datanode/*
hdfs namenode -format
```
3. Start distributed file cluster
```
start-dfs.sh
```
4. Start resource manager
```
start-yarn.sh
```
5. Check Java virtual machine process status
```
jps
```
6. Access browser interfaces
 * Name node information: [http://localhost:9870](http://localhost:9870)
 * Data node information: [http://localhost:9864](http://localhost:9864)
 * All applications: [http://localhost:8088](http://localhost:8088)
![HadoopUIscreenshot](https://github.com/tyknkd/hadoop/assets/78797823/cf1bcafc-86b2-4123-b620-7d4e46bd8399)

7. Create directory, copy, and view files on HDFS
```
hdfs dfs -mkdir /input
hdfs dfs -ls /
hdfs dfs -put ~/input/* /input
hdfs dfs -cat /input/*
```
8. Browse directory on interface: [http://localhost:9870/explorer.html](http://localhost:9870/explorer.html)
9. Stop Hadoop cluster
```
stop-dfs.sh
```
10. Stop resource manager
```
stop-yarn.sh
```
## MapReduce
1. Put files on HDFS for processing
```
hdfs dfs -mkdir /input
hdfs dfs -put ~/hadoop/etc/hadoop/*.xml /input
```
2. Use example MapReduce Java archive file to find strings starting with 'dfs'
```
hadoop jar ~/hadoop/share/hadoop/mapreduce/hadoop-mapreduce-examples-3.3.6.jar grep /input /output 'dfs[a-z.]+'
hdfs dfs -cat output/*
```

## Troubleshooting
* Missing name or data node
```
stop-dfs.sh
rm -R ~/hadoop/hdfs/namenode/*
rm -R ~/hadoop/hdfs/datanode/*
hdfs namenode -format
start-dfs.sh
jps
```

## References
Apache Hadoop Project. (2023, June 18). [Hadoop: Setting up a single node cluster](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-common/SingleCluster.html). Apache Software Foundation.

Kumar, R. (2022, October 28). [How to install and configure Hadoop on Ubuntu 20.04](https://tecadmin.net/install-hadoop-on-ubuntu-20-04/). Tec Admin.

Morumbasi, F. (2022, April 21). [Installing Hadoop on Ubuntu 20.04](https://medium.com/@festusmorumbasi/installing-hadoop-on-ubuntu-20-04-4610b6e0391e). Medium.

Shamar, S. (2023, March 17). [Install Hadoop on Ubuntu](https://learnubuntu.com/install-hadoop/). Learn Ubuntu.

Tutorials Point. (2014, December). [Hadoop Tutorial](https://www.tutorialspoint.com/hadoop/index.htm). Tutorials Point.
