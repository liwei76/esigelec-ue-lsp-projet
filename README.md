PROJECT SPARK-STREAMING 
===================================================================
Membre de notre équipe: LI Wei, DING Lingzhou, GONGANG KOUAMBOU Lorena Darcelle, BANGYA BIANDA Marie-Claire

Projet introduction
------------------------------------
This project mainly uses sparkstreaming + kafka + python or scala to simulate real-time data statistics.This project simulates real-time statistics of male and female purchases from an e-commerce website.We completed this project based on a tutorial from a Chinese university.

File explanation
-----------------------------------
* projetspark: Total folder
    * kafka: python running kafka
        * producer.py: kafka producer
        * consumer.py: kafka consumer
    * mycode: code de sparkstreaming
        * kafka_test.py: python code to run sparkstreaming
        * startup.sh: run python code
        * src/main/scala: scala code to run sparktreaming
        * simple.sbt: Packaging scala code
        * startscala.sh: run scala code

Data introduction
--------------------------------------------------
This data set compression package is the transaction data of the first 6 months of an e-commerce website in 2015.We use the user behavior log file user_log.csv.  
We simulated real-time data processing using the gender data in column 10 of the data, 0 male, 1 female, 2 unknown.

System and software requirements
--------------------------------------------------------------------
    Spark: 2.1.0
    Scala: 2.11.8
    kafka: 0.8.2.2
    Python: 3 & 2
* python library  
    Flask<br>
    Flask-SocketIO  
    kafka-python  
    kafka  

Install kafka
-----------------------------------------------
install kafka_2.11-0.10.2.0  
```bash
cd ~/Downloads
sudo tar -zxf kafka_2.11-0.10.2.0.tgz -C /usr/local
cd /usr/local
sudo mv kafka_2.11-0.10.2.0/ ./kafka
sudo chown -R hadoop ./kafka
```
start kafka
```bash
cd /usr/local/kafka
bin/zookeeper-server-start.sh config/zookeeper.properties &
bin/kafka-server-start.sh config/server.properties
```

Install Pycharm
-------------------------------------------------
```bash
cd ~
sudo tar -zxvf ~/Downlaods/pycharm-community-2016.3.2.tar.gz -C /usr/local  
cd /usr/local
sudo mv pycharm-community-2016.3.2 pycharm  
sudo chown -R hadoop ./pycharm 
```
    The directory structure of the Python project such as the folder sparktreaming
    The data directory stores user log data
    The scripts directory stores Kafka producers and consumers
    The static / js directory stores the js framework required by the front end
    The templates directory stores html pages

python using kafka
---------------------------------------------------
Creat `Producer.py` and `Consumer.py`in the scripts folder.  
Start kafka
```bash
cd /usr/local/kafka
bin/zookeeper-server-start.sh config/zookeeper.properties &
bin/kafka-server-start.sh config/server.properties
```
Run Producer.py first and then run Consumer.py  
We can see numbers in the terminal like  
0  
1  
2  
0  
1  
...  

Spark Streaming processes data in real time
---------------------------------------------
We write sparksteaming in three ways  
* Python2
* Python3
* Scala  

Sparkstreaming with python2
-------------------------------------------
Firstly download the codebase for Spark connection to Kafka.Then put the downloaded code base into the directory / usr / local / spark / jars.
```bash
sudo mv ~/Downloads/spark-streaming-kafka-0-8_2.11-2.1.0.jar /usr/local/spark/jars
 ```
 Copy all Kafka function libraries into spark
 ```bash
cd /usr/local/spark/jars
mkdir kafka
cd kafka
cp /usr/local/kafka/libs/* .
```
Add the path information of Kafka related jar packages to spark-env.sh
```bash
cd /usr/local/spark/conf
sudo vim spark-env.sh
export SPARK_DIST_CLASSPATH=$SPARK_DIST_CLASSPATH:/usr/local/spark/jars/kafka/*:/usr/local/kafka/libs/*
```
Create Spark project
```bash
cd /usr/local/spark
mkdir mycode
cd mycode
mkdir kafka
cd kafka
```
Then create a kafka_test.py file in the kafka directory.The specific code is in the folder mycode.  
Create a new startup.sh file in the / usr / local / spark / mycode / kafka directory
```bash
/usr/local/spark/bin/spark-submit /usr/local/spark/mycode/kafka/kafka_test.py 127.0.0.1:2181 1 sex 1
```
Run sparkstreaming code
```bash
sh startup.sh
```
Finally run `producer.py` and `consumer.py`

Sparkstreaming wiht python3
-----------------------------------------------------
The operation mode is the same as that of python2, except that certain configuration needs to be changed.We need to change pyspark's default python.  
Change spark_env in the conf directory.
```bash
cd /usr/local/spark/conf
sudo vim spark-env.sh
export PYSPARK_PYTHON=/usr/bin/python3
```
Change the pyspark file
```bash
cd /usr/local/spark/bin
vim pyspark

if [[ -z "$PYSPARK_PYTHON" ]]; then
  if [[ $PYSPARK_DRIVER_PYTHON == *ipython* && ! $WORKS_WITH_IPYTHON ]]; then
    echo "IPython requires Python 2.7+; please install python2.7 or set PYSPARK_PYTHON" 1>&2
    exit 1
  else
    PYSPARK_PYTHON=python3
  fi
fi
export PYSPARK_PYTHON
```
Change the version of PYSPARK_PYTHON to 3.And then we can use pyspark in python3.

Sparkstreaming with Scala
------------------------------------
Firstly download the codebase for Spark connection to Kafka.Then put the downloaded code base into the directory / usr / local / spark / jars.
```bash
sudo mv ~/Downloads/spark-streaming-kafka-0-8_2.11-2.1.0.jar /usr/local/spark/jars
 ```
 Copy all Kafka function libraries into spark
 ```bash
cd /usr/local/spark/jars
mkdir kafka
cd kafka
cp /usr/local/kafka/libs/* .
```
creat spark project
```bash
cd kafka
mkdir -p src/main/scala
```
Create two files under the src / main / scala file, one is used to set the log, and the other is the project project main file.The code is also in the folder mycode.  
Packaging scala running program
```bash
vim /usr/local/spark/mycode/kafka/simple.sbt

#like this
name := "Simple Project"
version := "1.0"
scalaVersion := "2.11.8"
libraryDependencies += "org.apache.spark" %% "spark-core" % "2.1.0"
libraryDependencies += "org.apache.spark" % "spark-streaming_2.11" % "2.1.0"
libraryDependencies += "org.apache.spark" % "spark-streaming-kafka-0-8_2.11" % "2.1.0"
libraryDependencies += "org.json4s" %% "json4s-jackson" % "3.2.11"

/usr/local/sbt/sbt package
```
Create a new startscala.sh file in the / usr / local / spark / mycode / kafka directory
```bash
vim /usr/local/spark/mycode/kafka/startscala.sh

#like this
/usr/local/spark/bin/spark-submit --driver-class-path /usr/local/spark/jars/*:/usr/local/spark/jars/kafka/* --class "org.apache.spark.examples.streaming.KafkaWordCount" /usr/local/spark/mycode/kafka/target/scala-2.11/simple-project_2.11-1.0.jar 127.0.0.1:2181 1 sex 1

```
Run spark-streaming
```bash
sh startscala.sh
```
Finally run `producer.py` and `consumer.py`


