This is a simple Kafka connector (formerly called a "copycat connector"). This repo is intended to be a simple skeleton, that you can copy/paste to get started with developing your own Kafka connector.

This repo contains a fork of the connect-file plugin from Kafka trunk. The code itself has not changed. However, I renamed the package, so that there is no chance that you will accidentally get the connect-file plugin from trunk.

This things specific to this repo are the build files, as well as the instructions in this README that tell you how to get started.

Instructions for building
-------------------------
```
$ git clone https://github.com/wushujames/kafka-connector-skeleton connect-file
$ (cd connect-file && ./gradlew build)
```
    
Instructions for running
------------------------
1.  Run zookeeper somehow.
    ```
    Here are instructions on how to run it using docker.
    $ docker run -d --name zookeeper -p 2181:2181 confluent/zookeeper
    ```

2.  Download and run kafka 0.9.0 locally by following the instructions at http://kafka.apache.org/documentation.html#quickstart
    ```
    $ curl -o kafka_2.11-0.9.0.0.tgz http://www.us.apache.org/dist/kafka/0.9.0.0/kafka_2.11-0.9.0.0.tgz
    $ tar xvfz kafka_2.11-0.9.0.0.tgz
    $ cd kafka_2.11-0.9.0.0
    $ ./bin/kafka-server-start.sh config/server.properties
    ```

3.  Run your copycat file-source plugin
    ```
    $ export CLASSPATH=/path/to/connect-file/build/libs/connect-file.jar
    $ kafka_2.11-0.9.0.0/bin/copycat-standalone.sh connect-file/copycat-standalone.properties  connect-file/connect-file-source.properties
    ```
    
4.  Write stuff to test.txt (that is the that this connector will read from, as configured in connect-file-source.properties)
    ```
    $ echo `date` >> test.txt
    ```
    
5.  Read the data out from the kafka topic named 'test' (that is the that this connector will write to, as configured in connect-file-source.properties)
    ```
    $ kafka_2.11-0.9.0.0/bin/kafka-console-consumer.sh  --zookeeper localhost:2181 --topic test
    {"schema":{"type":"string","optional":false},"payload":"Thu Oct 15 23:03:15 PDT 2015"}
    ```

6.  Run your copycat file-sink plugin
    ```
    $ export CLASSPATH=/path/to/connect-file/build/libs/connect-file.jar
    $ kafka_2.11-0.9.0.0/bin/copycat-standalone.sh connect-file/copycat-standalone.properties  connect-file/connect-file-sink.properties
    ```

7.  Check that the file-sink plugin has written the data to the file
    ```
    $ cat test.sink.txt
    Thu Oct 15 23:03:15 PDT 2015
    ```

