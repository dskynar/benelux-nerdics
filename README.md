# benelux-nerdics
some files for hackathon in Cloudera

## Setup
The demo environment is built on the Edge2AI environment here: (link). When the environment is installed, it will give you a link to a shell on the node. This is the starting point for commands below.

download the repo to a directory of your preference:

`git clone https://github.com/esteinholtz-cloudera/benelux-nerdics`

### NiFi

The universal CSV2JSON converter is built using jq. The converter expects the file to be placed in /home/centos/nifi-scripts

`mkdir /home/centos/nifi-scripts`

`mv csv2json.jq /home/centos/nifi-scripts` 

`chown -R nifi /home/centos/nifi-scripts`

`chmod -R 755 /home/centos/nifi-scripts`

Apart from this, you need to import the processing group template "Streamifier.xml" into Nifi, using the GUI. You will also need to instantiate a processing group from the template & start it. This processing group will take the source data, convert it into JSON Lines, and put these lines one by one as a stream to the Kafka topic "intrusion" 

### Dataset

the source data file is merged_dataset.csv. It's dimensions are 671,241 rows and 16 columns. It should be placed in a specific location in HDFS:

`hdfs dfs –put dataset.csv /user/admin/upload/intrusions.csv`

### Kafka
You will need to create a topic called "intrusion". Easiest in the SMM GUI.

## Talk track (NiFi & the streamifier)
NiFi is a graphic tool to process your files, much like ETL processsors. It is very modular in that it has 400 processors that can help you connect to any source and destination. For the manipulation underway, there are many converters that you can use.

The particular feature that we will show today in NiFi is the power of flow control. Between the processors are queues. When these get the color red, it means that the queue is full or near full, and a protocol called back-pressure is applied. This means that processing will slow down in the previous processors. Without this mechanism, there is no way that we could get an even flow of 10 messages per second. 

In this setup, we want to simulate a stream using our source data. We have an HDFS reader, picking up the data, a universal CSV2JSON converter that uses the underlying power of jq. The data is split up and rate controlled so that we get about 10 messages per second in our kafka stream.



