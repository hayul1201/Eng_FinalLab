# Part 2

## 1. Write a query to compare each active account’s balance to the average balance of all active accounts of the same type

## 2. Create an employee table in the metastore that contains the employee records stored in HDFS

## 3. Generate a table that contains all customers who have negative account balances.

## 4. LoudAcre Mobile has merged with another company located in California. Each company has a list of customers in different formats. Combine the two customer lists into a single dataset using an identical schema

### solution

<pre><code>
CREATE EXTERNAL TABLE employee1 (
id string,
fname string,
lname string,
address string,
city string,
state string,
zip string
) 
ROW FORMAT DELIMITED
FIELDS TERMINATED BY '\t;'
LOCATION 'hdfs://localhost:8020/user/training/problem4/data/employee1'
;

CREATE EXTERNAL TABLE employee2 (
id string,
unknown string,
lname string,
fname string,
address string,
city string,
state string,
zip string
) 
ROW FORMAT DELIMITED
FIELDS TERMINATED BY ','
LOCATION 'hdfs://localhost:8020/user/training/problem4/data/employee2'
;

CREATE EXTERNAL TABLE solution (
id string,
fname string,
lname string,
address string,
city string,
state string,
zip string
) 
ROW FORMAT DELIMITED
FIELDS TERMINATED BY '\t;'
LOCATION 'hdfs://localhost:8020/user/training/problem4/solution'
;

insert overwrite table solution
select * from (
select id, initcap(fname), initcap(lname), address, city, state, substr(zip, 1, 5) as zip
from employee1 a where a.state = 'CA'
union all
select id, initcap(fname), initcap(lname), address, city, state, substr(zip, 1, 5) as zip
from employee2 b where b.state = 'CA'
) aa ;

</code></pre>

### capture
![Screenshot](https://github.com/recrom/data_ingest_exercise/blob/master/img/problem4.PNG?raw=true)




## 5. The bank is making a Facebook group for the Palo Alto, CA branch. Generate a script that outputsthe customers and employees who live in Palo Alto, CA.


## 6. There are privacy concerns about the employee data that is stored on the cluster. Your task is to remove any age information from the employee data by creating a new table for the data analysts to query against


## 8. Use Sqoop to export customer data from HDFS into a MySQL database table. Place the data in the solution table in MySQL, which has been created and is currently empty.


### solution

<pre><code>
sqoop export --connect jdbc:mysql://localhost/problem8 --username cloudera --password cloudera --table solution --export-dir hdfs://localhost:8020/user/training/problem8/data/customer --input-fields-terminated-by '\t'
</code></pre>

<pre><code>
[training@localhost ~]$ sqoop export --connect jdbc:mysql://localhost/problem8 --username cloudera --password cloudera --table solution --export-dir hdfs://localhost:8020/user/training/problem8/data/customer --input-fields-terminated-by '\t'
19/05/21 22:59:56 INFO sqoop.Sqoop: Running Sqoop version: 1.4.6-cdh5.8.0
19/05/21 22:59:56 WARN tool.BaseSqoopTool: Setting your password on the command-line is insecure. Consider using -P instead.
19/05/21 22:59:56 INFO manager.MySQLManager: Preparing to use a MySQL streaming resultset.
19/05/21 22:59:56 INFO tool.CodeGenTool: Beginning code generation
19/05/21 22:59:56 INFO manager.SqlManager: Executing SQL statement: SELECT t.* FROM `solution` AS t LIMIT 1
19/05/21 22:59:56 INFO manager.SqlManager: Executing SQL statement: SELECT t.* FROM `solution` AS t LIMIT 1
19/05/21 22:59:56 INFO orm.CompilationManager: HADOOP_MAPRED_HOME is /usr/lib/hadoop-mapreduce
Note: /tmp/sqoop-training/compile/b1d66d909b8e6c0bac655bc4481f0b88/solution.java uses or overrides a deprecated API.
Note: Recompile with -Xlint:deprecation for details.
19/05/21 22:59:58 INFO orm.CompilationManager: Writing jar file: /tmp/sqoop-training/compile/b1d66d909b8e6c0bac655bc4481f0b88/solution.jar
19/05/21 22:59:58 INFO mapreduce.ExportJobBase: Beginning export of solution
19/05/21 22:59:58 INFO Configuration.deprecation: mapred.job.tracker is deprecated. Instead, use mapreduce.jobtracker.address
19/05/21 22:59:59 INFO Configuration.deprecation: mapred.jar is deprecated. Instead, use mapreduce.job.jar
19/05/21 22:59:59 INFO Configuration.deprecation: mapred.map.max.attempts is deprecated. Instead, use mapreduce.map.maxattempts
19/05/21 22:59:59 INFO Configuration.deprecation: mapred.reduce.tasks.speculative.execution is deprecated. Instead, use mapreduce.reduce.speculative
19/05/21 22:59:59 INFO Configuration.deprecation: mapred.map.tasks.speculative.execution is deprecated. Instead, use mapreduce.map.speculative
19/05/21 22:59:59 INFO Configuration.deprecation: mapred.map.tasks is deprecated. Instead, use mapreduce.job.maps
19/05/21 23:00:00 INFO client.RMProxy: Connecting to ResourceManager at /0.0.0.0:8032
19/05/21 23:00:00 WARN hdfs.DFSClient: Caught exception 
java.lang.InterruptedException
	at java.lang.Object.wait(Native Method)
	at java.lang.Thread.join(Thread.java:1245)
	at java.lang.Thread.join(Thread.java:1319)
	at org.apache.hadoop.hdfs.DFSOutputStream$DataStreamer.closeResponder(DFSOutputStream.java:862)
	at org.apache.hadoop.hdfs.DFSOutputStream$DataStreamer.endBlock(DFSOutputStream.java:600)
	at org.apache.hadoop.hdfs.DFSOutputStream$DataStreamer.run(DFSOutputStream.java:789)
19/05/21 23:00:00 WARN hdfs.DFSClient: Caught exception 
java.lang.InterruptedException
	at java.lang.Object.wait(Native Method)
	at java.lang.Thread.join(Thread.java:1245)
	at java.lang.Thread.join(Thread.java:1319)
	at org.apache.hadoop.hdfs.DFSOutputStream$DataStreamer.closeResponder(DFSOutputStream.java:862)
	at org.apache.hadoop.hdfs.DFSOutputStream$DataStreamer.closeInternal(DFSOutputStream.java:830)
	at org.apache.hadoop.hdfs.DFSOutputStream$DataStreamer.run(DFSOutputStream.java:826)
19/05/21 23:00:00 WARN hdfs.DFSClient: Caught exception 
java.lang.InterruptedException
	at java.lang.Object.wait(Native Method)
	at java.lang.Thread.join(Thread.java:1245)
	at java.lang.Thread.join(Thread.java:1319)
	at org.apache.hadoop.hdfs.DFSOutputStream$DataStreamer.closeResponder(DFSOutputStream.java:862)
	at org.apache.hadoop.hdfs.DFSOutputStream$DataStreamer.endBlock(DFSOutputStream.java:600)
	at org.apache.hadoop.hdfs.DFSOutputStream$DataStreamer.run(DFSOutputStream.java:789)
19/05/21 23:00:00 WARN hdfs.DFSClient: Caught exception 
java.lang.InterruptedException
	at java.lang.Object.wait(Native Method)
	at java.lang.Thread.join(Thread.java:1245)
	at java.lang.Thread.join(Thread.java:1319)
	at org.apache.hadoop.hdfs.DFSOutputStream$DataStreamer.closeResponder(DFSOutputStream.java:862)
	at org.apache.hadoop.hdfs.DFSOutputStream$DataStreamer.endBlock(DFSOutputStream.java:600)
	at org.apache.hadoop.hdfs.DFSOutputStream$DataStreamer.run(DFSOutputStream.java:789)
19/05/21 23:00:00 WARN hdfs.DFSClient: Caught exception 
java.lang.InterruptedException
	at java.lang.Object.wait(Native Method)
	at java.lang.Thread.join(Thread.java:1245)
	at java.lang.Thread.join(Thread.java:1319)
	at org.apache.hadoop.hdfs.DFSOutputStream$DataStreamer.closeResponder(DFSOutputStream.java:862)
	at org.apache.hadoop.hdfs.DFSOutputStream$DataStreamer.endBlock(DFSOutputStream.java:600)
	at org.apache.hadoop.hdfs.DFSOutputStream$DataStreamer.run(DFSOutputStream.java:789)
19/05/21 23:00:00 WARN hdfs.DFSClient: Caught exception 
java.lang.InterruptedException
	at java.lang.Object.wait(Native Method)
	at java.lang.Thread.join(Thread.java:1245)
	at java.lang.Thread.join(Thread.java:1319)
	at org.apache.hadoop.hdfs.DFSOutputStream$DataStreamer.closeResponder(DFSOutputStream.java:862)
	at org.apache.hadoop.hdfs.DFSOutputStream$DataStreamer.endBlock(DFSOutputStream.java:600)
	at org.apache.hadoop.hdfs.DFSOutputStream$DataStreamer.run(DFSOutputStream.java:789)
19/05/21 23:00:01 WARN hdfs.DFSClient: Caught exception 
java.lang.InterruptedException
	at java.lang.Object.wait(Native Method)
	at java.lang.Thread.join(Thread.java:1245)
	at java.lang.Thread.join(Thread.java:1319)
	at org.apache.hadoop.hdfs.DFSOutputStream$DataStreamer.closeResponder(DFSOutputStream.java:862)
	at org.apache.hadoop.hdfs.DFSOutputStream$DataStreamer.endBlock(DFSOutputStream.java:600)
	at org.apache.hadoop.hdfs.DFSOutputStream$DataStreamer.run(DFSOutputStream.java:789)
19/05/21 23:00:01 WARN hdfs.DFSClient: Caught exception 
java.lang.InterruptedException
	at java.lang.Object.wait(Native Method)
	at java.lang.Thread.join(Thread.java:1245)
	at java.lang.Thread.join(Thread.java:1319)
	at org.apache.hadoop.hdfs.DFSOutputStream$DataStreamer.closeResponder(DFSOutputStream.java:862)
	at org.apache.hadoop.hdfs.DFSOutputStream$DataStreamer.endBlock(DFSOutputStream.java:600)
	at org.apache.hadoop.hdfs.DFSOutputStream$DataStreamer.run(DFSOutputStream.java:789)
19/05/21 23:00:01 WARN hdfs.DFSClient: Caught exception 
java.lang.InterruptedException
	at java.lang.Object.wait(Native Method)
	at java.lang.Thread.join(Thread.java:1245)
	at java.lang.Thread.join(Thread.java:1319)
	at org.apache.hadoop.hdfs.DFSOutputStream$DataStreamer.closeResponder(DFSOutputStream.java:862)
	at org.apache.hadoop.hdfs.DFSOutputStream$DataStreamer.endBlock(DFSOutputStream.java:600)
	at org.apache.hadoop.hdfs.DFSOutputStream$DataStreamer.run(DFSOutputStream.java:789)
19/05/21 23:00:01 WARN hdfs.DFSClient: Caught exception 
java.lang.InterruptedException
	at java.lang.Object.wait(Native Method)
	at java.lang.Thread.join(Thread.java:1245)
	at java.lang.Thread.join(Thread.java:1319)
	at org.apache.hadoop.hdfs.DFSOutputStream$DataStreamer.closeResponder(DFSOutputStream.java:862)
	at org.apache.hadoop.hdfs.DFSOutputStream$DataStreamer.endBlock(DFSOutputStream.java:600)
	at org.apache.hadoop.hdfs.DFSOutputStream$DataStreamer.run(DFSOutputStream.java:789)
19/05/21 23:00:01 WARN hdfs.DFSClient: Caught exception 
java.lang.InterruptedException
	at java.lang.Object.wait(Native Method)
	at java.lang.Thread.join(Thread.java:1245)
	at java.lang.Thread.join(Thread.java:1319)
	at org.apache.hadoop.hdfs.DFSOutputStream$DataStreamer.closeResponder(DFSOutputStream.java:862)
	at org.apache.hadoop.hdfs.DFSOutputStream$DataStreamer.closeInternal(DFSOutputStream.java:830)
	at org.apache.hadoop.hdfs.DFSOutputStream$DataStreamer.run(DFSOutputStream.java:826)
19/05/21 23:00:01 WARN hdfs.DFSClient: Caught exception 
java.lang.InterruptedException
	at java.lang.Object.wait(Native Method)
	at java.lang.Thread.join(Thread.java:1245)
	at java.lang.Thread.join(Thread.java:1319)
	at org.apache.hadoop.hdfs.DFSOutputStream$DataStreamer.closeResponder(DFSOutputStream.java:862)
	at org.apache.hadoop.hdfs.DFSOutputStream$DataStreamer.closeInternal(DFSOutputStream.java:830)
	at org.apache.hadoop.hdfs.DFSOutputStream$DataStreamer.run(DFSOutputStream.java:826)
19/05/21 23:00:01 WARN hdfs.DFSClient: Caught exception 
java.lang.InterruptedException
	at java.lang.Object.wait(Native Method)
	at java.lang.Thread.join(Thread.java:1245)
	at java.lang.Thread.join(Thread.java:1319)
	at org.apache.hadoop.hdfs.DFSOutputStream$DataStreamer.closeResponder(DFSOutputStream.java:862)
	at org.apache.hadoop.hdfs.DFSOutputStream$DataStreamer.endBlock(DFSOutputStream.java:600)
	at org.apache.hadoop.hdfs.DFSOutputStream$DataStreamer.run(DFSOutputStream.java:789)
19/05/21 23:00:01 WARN hdfs.DFSClient: Caught exception 
java.lang.InterruptedException
	at java.lang.Object.wait(Native Method)
	at java.lang.Thread.join(Thread.java:1245)
	at java.lang.Thread.join(Thread.java:1319)
	at org.apache.hadoop.hdfs.DFSOutputStream$DataStreamer.closeResponder(DFSOutputStream.java:862)
	at org.apache.hadoop.hdfs.DFSOutputStream$DataStreamer.endBlock(DFSOutputStream.java:600)
	at org.apache.hadoop.hdfs.DFSOutputStream$DataStreamer.run(DFSOutputStream.java:789)
19/05/21 23:00:01 WARN hdfs.DFSClient: Caught exception 
java.lang.InterruptedException
	at java.lang.Object.wait(Native Method)
	at java.lang.Thread.join(Thread.java:1245)
	at java.lang.Thread.join(Thread.java:1319)
	at org.apache.hadoop.hdfs.DFSOutputStream$DataStreamer.closeResponder(DFSOutputStream.java:862)
	at org.apache.hadoop.hdfs.DFSOutputStream$DataStreamer.closeInternal(DFSOutputStream.java:830)
	at org.apache.hadoop.hdfs.DFSOutputStream$DataStreamer.run(DFSOutputStream.java:826)
19/05/21 23:00:01 WARN hdfs.DFSClient: Caught exception 
java.lang.InterruptedException
	at java.lang.Object.wait(Native Method)
	at java.lang.Thread.join(Thread.java:1245)
	at java.lang.Thread.join(Thread.java:1319)
	at org.apache.hadoop.hdfs.DFSOutputStream$DataStreamer.closeResponder(DFSOutputStream.java:862)
	at org.apache.hadoop.hdfs.DFSOutputStream$DataStreamer.endBlock(DFSOutputStream.java:600)
	at org.apache.hadoop.hdfs.DFSOutputStream$DataStreamer.run(DFSOutputStream.java:789)
19/05/21 23:00:01 WARN hdfs.DFSClient: Caught exception 
java.lang.InterruptedException
	at java.lang.Object.wait(Native Method)
	at java.lang.Thread.join(Thread.java:1245)
	at java.lang.Thread.join(Thread.java:1319)
	at org.apache.hadoop.hdfs.DFSOutputStream$DataStreamer.closeResponder(DFSOutputStream.java:862)
	at org.apache.hadoop.hdfs.DFSOutputStream$DataStreamer.endBlock(DFSOutputStream.java:600)
	at org.apache.hadoop.hdfs.DFSOutputStream$DataStreamer.run(DFSOutputStream.java:789)
19/05/21 23:00:01 WARN hdfs.DFSClient: Caught exception 
java.lang.InterruptedException
	at java.lang.Object.wait(Native Method)
	at java.lang.Thread.join(Thread.java:1245)
	at java.lang.Thread.join(Thread.java:1319)
	at org.apache.hadoop.hdfs.DFSOutputStream$DataStreamer.closeResponder(DFSOutputStream.java:862)
	at org.apache.hadoop.hdfs.DFSOutputStream$DataStreamer.endBlock(DFSOutputStream.java:600)
	at org.apache.hadoop.hdfs.DFSOutputStream$DataStreamer.run(DFSOutputStream.java:789)
19/05/21 23:00:01 WARN hdfs.DFSClient: Caught exception 
java.lang.InterruptedException
	at java.lang.Object.wait(Native Method)
	at java.lang.Thread.join(Thread.java:1245)
	at java.lang.Thread.join(Thread.java:1319)
	at org.apache.hadoop.hdfs.DFSOutputStream$DataStreamer.closeResponder(DFSOutputStream.java:862)
	at org.apache.hadoop.hdfs.DFSOutputStream$DataStreamer.endBlock(DFSOutputStream.java:600)
	at org.apache.hadoop.hdfs.DFSOutputStream$DataStreamer.run(DFSOutputStream.java:789)
19/05/21 23:00:01 WARN hdfs.DFSClient: Caught exception 
java.lang.InterruptedException
	at java.lang.Object.wait(Native Method)
	at java.lang.Thread.join(Thread.java:1245)
	at java.lang.Thread.join(Thread.java:1319)
	at org.apache.hadoop.hdfs.DFSOutputStream$DataStreamer.closeResponder(DFSOutputStream.java:862)
	at org.apache.hadoop.hdfs.DFSOutputStream$DataStreamer.endBlock(DFSOutputStream.java:600)
	at org.apache.hadoop.hdfs.DFSOutputStream$DataStreamer.run(DFSOutputStream.java:789)
19/05/21 23:00:01 WARN hdfs.DFSClient: Caught exception 
java.lang.InterruptedException
	at java.lang.Object.wait(Native Method)
	at java.lang.Thread.join(Thread.java:1245)
	at java.lang.Thread.join(Thread.java:1319)
	at org.apache.hadoop.hdfs.DFSOutputStream$DataStreamer.closeResponder(DFSOutputStream.java:862)
	at org.apache.hadoop.hdfs.DFSOutputStream$DataStreamer.endBlock(DFSOutputStream.java:600)
	at org.apache.hadoop.hdfs.DFSOutputStream$DataStreamer.run(DFSOutputStream.java:789)
19/05/21 23:00:01 WARN hdfs.DFSClient: Caught exception 
java.lang.InterruptedException
	at java.lang.Object.wait(Native Method)
	at java.lang.Thread.join(Thread.java:1245)
	at java.lang.Thread.join(Thread.java:1319)
	at org.apache.hadoop.hdfs.DFSOutputStream$DataStreamer.closeResponder(DFSOutputStream.java:862)
	at org.apache.hadoop.hdfs.DFSOutputStream$DataStreamer.closeInternal(DFSOutputStream.java:830)
	at org.apache.hadoop.hdfs.DFSOutputStream$DataStreamer.run(DFSOutputStream.java:826)
19/05/21 23:00:01 WARN hdfs.DFSClient: Caught exception 
java.lang.InterruptedException
	at java.lang.Object.wait(Native Method)
	at java.lang.Thread.join(Thread.java:1245)
	at java.lang.Thread.join(Thread.java:1319)
	at org.apache.hadoop.hdfs.DFSOutputStream$DataStreamer.closeResponder(DFSOutputStream.java:862)
	at org.apache.hadoop.hdfs.DFSOutputStream$DataStreamer.endBlock(DFSOutputStream.java:600)
	at org.apache.hadoop.hdfs.DFSOutputStream$DataStreamer.run(DFSOutputStream.java:789)
19/05/21 23:00:01 WARN hdfs.DFSClient: Caught exception 
java.lang.InterruptedException
	at java.lang.Object.wait(Native Method)
	at java.lang.Thread.join(Thread.java:1245)
	at java.lang.Thread.join(Thread.java:1319)
	at org.apache.hadoop.hdfs.DFSOutputStream$DataStreamer.closeResponder(DFSOutputStream.java:862)
	at org.apache.hadoop.hdfs.DFSOutputStream$DataStreamer.endBlock(DFSOutputStream.java:600)
	at org.apache.hadoop.hdfs.DFSOutputStream$DataStreamer.run(DFSOutputStream.java:789)
19/05/21 23:00:01 WARN hdfs.DFSClient: Caught exception 
java.lang.InterruptedException
	at java.lang.Object.wait(Native Method)
	at java.lang.Thread.join(Thread.java:1245)
	at java.lang.Thread.join(Thread.java:1319)
	at org.apache.hadoop.hdfs.DFSOutputStream$DataStreamer.closeResponder(DFSOutputStream.java:862)
	at org.apache.hadoop.hdfs.DFSOutputStream$DataStreamer.closeInternal(DFSOutputStream.java:830)
	at org.apache.hadoop.hdfs.DFSOutputStream$DataStreamer.run(DFSOutputStream.java:826)
19/05/21 23:00:01 WARN hdfs.DFSClient: Caught exception 
java.lang.InterruptedException
	at java.lang.Object.wait(Native Method)
	at java.lang.Thread.join(Thread.java:1245)
	at java.lang.Thread.join(Thread.java:1319)
	at org.apache.hadoop.hdfs.DFSOutputStream$DataStreamer.closeResponder(DFSOutputStream.java:862)
	at org.apache.hadoop.hdfs.DFSOutputStream$DataStreamer.endBlock(DFSOutputStream.java:600)
	at org.apache.hadoop.hdfs.DFSOutputStream$DataStreamer.run(DFSOutputStream.java:789)
19/05/21 23:00:01 WARN hdfs.DFSClient: Caught exception 
java.lang.InterruptedException
	at java.lang.Object.wait(Native Method)
	at java.lang.Thread.join(Thread.java:1245)
	at java.lang.Thread.join(Thread.java:1319)
	at org.apache.hadoop.hdfs.DFSOutputStream$DataStreamer.closeResponder(DFSOutputStream.java:862)
	at org.apache.hadoop.hdfs.DFSOutputStream$DataStreamer.endBlock(DFSOutputStream.java:600)
	at org.apache.hadoop.hdfs.DFSOutputStream$DataStreamer.run(DFSOutputStream.java:789)
19/05/21 23:00:01 WARN hdfs.DFSClient: Caught exception 
java.lang.InterruptedException
	at java.lang.Object.wait(Native Method)
	at java.lang.Thread.join(Thread.java:1245)
	at java.lang.Thread.join(Thread.java:1319)
	at org.apache.hadoop.hdfs.DFSOutputStream$DataStreamer.closeResponder(DFSOutputStream.java:862)
	at org.apache.hadoop.hdfs.DFSOutputStream$DataStreamer.endBlock(DFSOutputStream.java:600)
	at org.apache.hadoop.hdfs.DFSOutputStream$DataStreamer.run(DFSOutputStream.java:789)
19/05/21 23:00:01 WARN hdfs.DFSClient: Caught exception 
java.lang.InterruptedException
	at java.lang.Object.wait(Native Method)
	at java.lang.Thread.join(Thread.java:1245)
	at java.lang.Thread.join(Thread.java:1319)
	at org.apache.hadoop.hdfs.DFSOutputStream$DataStreamer.closeResponder(DFSOutputStream.java:862)
	at org.apache.hadoop.hdfs.DFSOutputStream$DataStreamer.endBlock(DFSOutputStream.java:600)
	at org.apache.hadoop.hdfs.DFSOutputStream$DataStreamer.run(DFSOutputStream.java:789)
19/05/21 23:00:01 WARN hdfs.DFSClient: Caught exception 
java.lang.InterruptedException
	at java.lang.Object.wait(Native Method)
	at java.lang.Thread.join(Thread.java:1245)
	at java.lang.Thread.join(Thread.java:1319)
	at org.apache.hadoop.hdfs.DFSOutputStream$DataStreamer.closeResponder(DFSOutputStream.java:862)
	at org.apache.hadoop.hdfs.DFSOutputStream$DataStreamer.endBlock(DFSOutputStream.java:600)
	at org.apache.hadoop.hdfs.DFSOutputStream$DataStreamer.run(DFSOutputStream.java:789)
19/05/21 23:00:01 WARN hdfs.DFSClient: Caught exception 
java.lang.InterruptedException
	at java.lang.Object.wait(Native Method)
	at java.lang.Thread.join(Thread.java:1245)
	at java.lang.Thread.join(Thread.java:1319)
	at org.apache.hadoop.hdfs.DFSOutputStream$DataStreamer.closeResponder(DFSOutputStream.java:862)
	at org.apache.hadoop.hdfs.DFSOutputStream$DataStreamer.endBlock(DFSOutputStream.java:600)
	at org.apache.hadoop.hdfs.DFSOutputStream$DataStreamer.run(DFSOutputStream.java:789)
19/05/21 23:00:01 WARN hdfs.DFSClient: Caught exception 
java.lang.InterruptedException
	at java.lang.Object.wait(Native Method)
	at java.lang.Thread.join(Thread.java:1245)
	at java.lang.Thread.join(Thread.java:1319)
	at org.apache.hadoop.hdfs.DFSOutputStream$DataStreamer.closeResponder(DFSOutputStream.java:862)
	at org.apache.hadoop.hdfs.DFSOutputStream$DataStreamer.endBlock(DFSOutputStream.java:600)
	at org.apache.hadoop.hdfs.DFSOutputStream$DataStreamer.run(DFSOutputStream.java:789)
19/05/21 23:00:01 INFO input.FileInputFormat: Total input paths to process : 1
19/05/21 23:00:01 INFO input.FileInputFormat: Total input paths to process : 1
19/05/21 23:00:01 WARN hdfs.DFSClient: Caught exception 
java.lang.InterruptedException
	at java.lang.Object.wait(Native Method)
	at java.lang.Thread.join(Thread.java:1245)
	at java.lang.Thread.join(Thread.java:1319)
	at org.apache.hadoop.hdfs.DFSOutputStream$DataStreamer.closeResponder(DFSOutputStream.java:862)
	at org.apache.hadoop.hdfs.DFSOutputStream$DataStreamer.endBlock(DFSOutputStream.java:600)
	at org.apache.hadoop.hdfs.DFSOutputStream$DataStreamer.run(DFSOutputStream.java:789)
19/05/21 23:00:01 INFO mapreduce.JobSubmitter: number of splits:4
19/05/21 23:00:01 INFO Configuration.deprecation: mapred.map.tasks.speculative.execution is deprecated. Instead, use mapreduce.map.speculative
19/05/21 23:00:01 INFO mapreduce.JobSubmitter: Submitting tokens for job: job_1558496972248_0026
19/05/21 23:00:02 INFO impl.YarnClientImpl: Submitted application application_1558496972248_0026
19/05/21 23:00:02 INFO mapreduce.Job: The url to track the job: http://localhost:8088/proxy/application_1558496972248_0026/
19/05/21 23:00:02 INFO mapreduce.Job: Running job: job_1558496972248_0026
19/05/21 23:00:09 INFO mapreduce.Job: Job job_1558496972248_0026 running in uber mode : false
19/05/21 23:00:09 INFO mapreduce.Job:  map 0% reduce 0%
19/05/21 23:00:26 INFO mapreduce.Job:  map 100% reduce 0%
19/05/21 23:00:26 INFO mapreduce.Job: Job job_1558496972248_0026 completed successfully
19/05/21 23:00:26 INFO mapreduce.Job: Counters: 30
	File System Counters
		FILE: Number of bytes read=0
		FILE: Number of bytes written=571200
		FILE: Number of read operations=0
		FILE: Number of large read operations=0
		FILE: Number of write operations=0
		HDFS: Number of bytes read=18887
		HDFS: Number of bytes written=0
		HDFS: Number of read operations=19
		HDFS: Number of large read operations=0
		HDFS: Number of write operations=0
	Job Counters 
		Launched map tasks=4
		Data-local map tasks=4
		Total time spent by all maps in occupied slots (ms)=117774
		Total time spent by all reduces in occupied slots (ms)=0
		Total time spent by all map tasks (ms)=58887
		Total vcore-seconds taken by all map tasks=58887
		Total megabyte-seconds taken by all map tasks=30150144
	Map-Reduce Framework
		Map input records=100
		Map output records=100
		Input split bytes=676
		Spilled Records=0
		Failed Shuffles=0
		Merged Map outputs=0
		GC time elapsed (ms)=570
		CPU time spent (ms)=2520
		Physical memory (bytes) snapshot=480022528
		Virtual memory (bytes) snapshot=9048915968
		Total committed heap usage (bytes)=251920384
	File Input Format Counters 
		Bytes Read=0
	File Output Format Counters 
		Bytes Written=0
19/05/21 23:00:26 INFO mapreduce.ExportJobBase: Transferred 18.4443 KB in 26.7283 seconds (706.6292 bytes/sec)
19/05/21 23:00:26 INFO mapreduce.ExportJobBase: Exported 100 records.
[training@localhost ~]$ 
</code></pre>


### output

<pre><code>


</code></pre>




## 2. Create an employee table in the metastore that contains the employee records stored in HDFS

### solution

<pre><code>
mysql> select * from solution;
+----------+-----------+------------+--------------------------------------+------------------+-------+-------+------------+
| id       | fname     | lname      | address                              | city             | state | zip   | birthday   |
+----------+-----------+------------+--------------------------------------+------------------+-------+-------+------------+
| 10000000 | Deanna    | Lane       | 900-1514 Vitae, Rd.                  | Lafayette        | LA    | 97827 | 08/31/2016 |
| 10000001 | Hall      | Garrett    | 9656 Urna Avenue                     | Tucson           | AZ    | 86511 | 08/24/2016 |
| 10000002 | Lucian    | Dotson     | P.O. Box 277, 4808 Fusce St.         | Seattle          | WA    | 57731 | 08/12/2016 |
| 10000003 | Yuri      | Sherman    | Ap #399-8275 Molestie Road           | Kapolei          | HI    | 16943 | 08/26/2016 |
| 10000004 | Jaime     | Griffin    | Ap #647-2123 Quis Rd.                | Madison          | WI    | 51394 | 08/13/2016 |
| 10000005 | Zorita    | Weber      | 747-9424 Orci, Av.                   | Hattiesburg      | MS    | 90262 | 08/09/2016 |
| 10000006 | Mara      | Meadows    | 517-4594 Ac, Rd.                     | Huntsville       | AL    | 35374 | 08/11/2016 |
| 10000007 | Evan      | Richard    | P.O. Box 223, 8182 Non, Av.          | College          | AK    | 99682 | 08/25/2016 |
| 10000008 | Briar     | Anderson   | Ap #548-6452 Nunc Road               | Cleveland        | OH    | 90704 | 08/18/2016 |
| 10000009 | Cole      | Odom       | P.O. Box 962, 2496 Sodales St.       | Boston           | MA    | 27282 | 08/21/2016 |
| 10000010 | Edward    | Dorsey     | 8473 Aliquam Rd.                     | Montgomery       | AL    | 36204 | 08/13/2016 |
| 10000011 | Amery     | Mason      | Ap #231-8154 Dictum Rd.              | Memphis          | TN    | 58035 | 08/09/2016 |
| 10000012 | Keaton    | Warren     | Ap #173-7618 Tellus Rd.              | Tallahassee      | FL    | 19431 | 08/20/2016 |
| 10000013 | Erin      | Ford       | 112-1682 Mauris Rd.                  | Nashville        | TN    | 51731 | 08/26/2016 |
| 10000014 | Madison   | Oconnor    | 2326 Velit Rd.                       | Dover            | DE    | 93480 | 08/19/2016 |
| 10000015 | Baker     | Hodges     | P.O. Box 932, 4163 Cursus Rd.        | Salem            | OR    | 16709 | 08/25/2016 |
| 10000016 | Leo       | Kane       | Ap #832-2102 Malesuada Rd.           | Glendale         | AZ    | 86065 | 08/14/2016 |
| 10000017 | Kyra      | Hays       | P.O. Box 311, 5471 Faucibus Road     | Springfield      | IL    | 10229 | 08/21/2016 |
| 10000018 | Wallace   | Saunders   | Ap #462-9715 Massa. Road             | Nampa            | ID    | 46348 | 08/16/2016 |
| 10000019 | Anjolie   | Whitaker   | 7004 Dis St.                         | Bloomington      | MN    | 69004 | 08/13/2016 |
| 10000020 | Price     | Washington | 911-5979 Non, Ave                    | Bloomington      | MN    | 14550 | 08/11/2016 |
| 10000021 | Zeph      | Mcclain    | Ap #248-9912 Elementum, St.          | Seattle          | WA    | 52142 | 08/25/2016 |
| 10000022 | Sopoline  | Hall       | P.O. Box 860, 8554 Sed Street        | Provo            | UT    | 28779 | 08/20/2016 |
| 10000023 | Riley     | Mcfadden   | 539-6573 Vitae, Avenue               | Toledo           | OH    | 24078 | 08/08/2016 |
| 10000024 | Kareem    | Houston    | P.O. Box 522, 594 Ligula. St.        | Tucson           | AZ    | 86555 | 08/12/2016 |
| 10000025 | Fatima    | Rowe       | 515-1899 Duis St.                    | Oklahoma City    | OK    | 66663 | 08/21/2016 |
| 10000026 | Fallon    | Middleton  | 4272 Sed Ave                         | Fort Smith       | AR    | 71864 | 08/27/2016 |
| 10000027 | Quyn      | Cleveland  | Ap #105-4585 Ipsum Street            | Cincinnati       | OH    | 70964 | 08/28/2016 |
| 10000028 | Hedley    | Watts      | Ap #936-6612 Sed St.                 | Philadelphia     | PA    | 22226 | 08/25/2016 |
| 10000029 | Ria       | Burris     | 960-5039 Morbi Road                  | Billings         | MT    | 98770 | 08/29/2016 |
| 10000030 | Haley     | Wells      | 540-3916 Aliquam Rd.                 | Tallahassee      | FL    | 90040 | 08/21/2016 |
| 10000031 | Jayme     | Randolph   | 629-2721 Nonummy Street              | Wilmington       | DE    | 82890 | 08/29/2016 |
| 10000032 | Catherine | Anthony    | 5393 Id Rd.                          | Juneau           | AK    | 99679 | 08/25/2016 |
| 10000033 | Hayden    | Roman      | 168-9118 Lobortis, St.               | Hillsboro        | OR    | 38133 | 08/14/2016 |
| 10000034 | Edan      | Bartlett   | 7922 Sed Ave                         | Indianapolis     | IN    | 77069 | 08/10/2016 |
| 10000035 | Marny     | Benton     | Ap #879-9141 Libero Rd.              | Nampa            | ID    | 76773 | 08/28/2016 |
| 10000036 | Russell   | Barton     | 7206 Et, Rd.                         | Newport News     | VA    | 69335 | 08/20/2016 |
| 10000037 | Jillian   | Alford     | P.O. Box 310, 6428 Vivamus Road      | Oklahoma City    | OK    | 55528 | 08/23/2016 |
| 10000038 | Tashya    | Martin     | 527-7777 Dui, Av.                    | Nampa            | ID    | 74158 | 08/17/2016 |
| 10000039 | Willow    | Mckee      | Ap #631-9424 Nonummy Rd.             | Pocatello        | ID    | 37595 | 08/30/2016 |
| 10000040 | Ryder     | Fischer    | 6724 Risus. Av.                      | Eugene           | OR    | 21899 | 08/22/2016 |
| 10000041 | Barry     | Bullock    | 2559 Adipiscing St.                  | South Burlington | VT    | 79768 | 08/15/2016 |
| 10000042 | Rashad    | Adkins     | Ap #362-1076 Libero Avenue           | Joliet           | IL    | 52159 | 08/09/2016 |
| 10000043 | Clio      | Mcdonald   | P.O. Box 689, 1175 Lacus, St.        | Harrisburg       | PA    | 59608 | 08/21/2016 |
| 10000044 | Anthony   | Burgess    | P.O. Box 329, 9049 Adipiscing Avenue | Fayetteville     | AR    | 72087 | 08/24/2016 |
| 10000045 | Sierra    | Newman     | 1585 Interdum. Road                  | Kearney          | NE    | 61539 | 08/28/2016 |
| 10000046 | Lysandra  | Woods      | P.O. Box 752, 9187 Nec, Road         | Bozeman          | MT    | 77258 | 08/17/2016 |
| 10000047 | Althea    | Flores     | P.O. Box 733, 1974 Parturient Rd.    | Savannah         | GA    | 10052 | 08/12/2016 |
| 10000048 | Ignatius  | Valencia   | 9023 Ac Ave                          | Chandler         | AZ    | 85748 | 08/12/2016 |
| 10000049 | Nomlanga  | Hurley     | Ap #273-8329 Ipsum Road              | Idaho Falls      | ID    | 84584 | 08/21/2016 |
| 10000050 | Rudyard   | Good       | Ap #139-6866 Justo Av.               | Tallahassee      | FL    | 71696 | 08/11/2016 |
| 10000051 | Ora       | Franco     | 358-7930 Vulputate St.               | Racine           | WI    | 70136 | 08/17/2016 |
| 10000052 | Keith     | Cortez     | P.O. Box 106, 4349 Aliquet Rd.       | Wilmington       | DE    | 76164 | 08/21/2016 |
| 10000053 | Abraham   | Strong     | 6437 Libero Road                     | Newark           | DE    | 39913 | 08/31/2016 |
| 10000054 | Lysandra  | Leon       | Ap #622-8995 Fringilla Road          | Wilmington       | DE    | 49862 | 08/22/2016 |
| 10000055 | Nola      | Cooke      | P.O. Box 645, 2670 Nibh Street       | Fayetteville     | AR    | 71287 | 08/26/2016 |
| 10000056 | Vaughan   | Fry        | 987 Eu Ave                           | Fairbanks        | AK    | 99974 | 08/14/2016 |
| 10000057 | Quinlan   | Ortega     | P.O. Box 442, 4630 Aliquet, Avenue   | Naperville       | IL    | 45664 | 08/11/2016 |
| 10000058 | Mollie    | Mcintosh   | P.O. Box 775, 5333 Nisi Avenue       | Independence     | MO    | 23154 | 08/15/2016 |
| 10000059 | Lester    | Morrow     | 150-9438 Diam. Rd.                   | South Bend       | IN    | 85861 | 08/17/2016 |
| 10000060 | Catherine | Gay        | 912-383 Morbi Av.                    | Saint Louis      | MO    | 56765 | 08/23/2016 |
| 10000061 | Todd      | Ochoa      | P.O. Box 397, 8055 Pellentesque. Ave | Cedar Rapids     | IA    | 91483 | 08/23/2016 |
| 10000062 | Brenna    | Savage     | 643-6376 Dictum. Ave                 | Columbus         | OH    | 26095 | 08/22/2016 |
| 10000063 | Carson    | Hardin     | Ap #407-1753 Arcu. St.               | Sandy            | UT    | 53101 | 08/11/2016 |
| 10000064 | Walker    | Holt       | 429-5561 Sed Road                    | Lincoln          | NE    | 38701 | 08/13/2016 |
| 10000065 | Zoe       | Boyle      | 2055 Semper, Road                    | Oklahoma City    | OK    | 85467 | 08/28/2016 |
| 10000066 | Harper    | Mcmahon    | 6828 Aliquet St.                     | Missoula         | MT    | 74260 | 08/13/2016 |
| 10000067 | Samantha  | Holt       | 5659 Ultrices. Rd.                   | Anchorage        | AK    | 99847 | 08/29/2016 |
| 10000068 | Kasimir   | Davenport  | Ap #259-482 Sed Ave                  | Chicago          | IL    | 90335 | 08/14/2016 |
| 10000069 | Priscilla | Hill       | Ap #608-9666 Diam Rd.                | Essex            | VT    | 14212 | 08/21/2016 |
| 10000070 | Alec      | Pennington | 8889 Eget Rd.                        | Austin           | TX    | 57251 | 08/24/2016 |
| 10000071 | Emma      | Pate       | 6541 Malesuada. Street               | Cambridge        | MA    | 26956 | 08/26/2016 |
| 10000072 | Maya      | Aguirre    | 1814 Arcu. St.                       | Fairbanks        | AK    | 99806 | 08/10/2016 |
| 10000073 | Brennan   | Roach      | 718-4629 Amet Street                 | Cedar Rapids     | IA    | 66071 | 08/31/2016 |
| 10000074 | Colton    | White      | 3537 Curae; Avenue                   | Laramie          | WY    | 91607 | 08/18/2016 |
| 10000075 | Judith    | Parks      | 247-7134 Vel St.                     | Anchorage        | AK    | 99949 | 08/25/2016 |
| 10000076 | Aquila    | Ramos      | 1409 A St.                           | Olathe           | KS    | 40726 | 08/13/2016 |
| 10000077 | Portia    | Gutierrez  | 404-2344 Et St.                      | Baltimore        | MD    | 71446 | 08/12/2016 |
| 10000078 | Curran    | Bauer      | Ap #129-2697 Posuere Street          | Tallahassee      | FL    | 22993 | 08/22/2016 |
| 10000079 | Igor      | Logan      | Ap #577-9985 Risus. Road             | Birmingham       | AL    | 36866 | 08/27/2016 |
| 10000080 | Marcia    | Duffy      | P.O. Box 395, 7686 Nunc, Av.         | Missoula         | MT    | 16985 | 08/18/2016 |
| 10000081 | Imelda    | Meyers     | 2718 Lorem St.                       | Racine           | WI    | 26227 | 08/10/2016 |
| 10000082 | Nell      | Mckinney   | 3645 Amet Rd.                        | Mobile           | AL    | 35231 | 08/18/2016 |
| 10000083 | Reagan    | Poole      | 9091 Ipsum Rd.                       | Jacksonville     | FL    | 22971 | 08/24/2016 |
| 10000084 | Jemima    | Ramsey     | P.O. Box 139, 8390 Lacus. Ave        | Milwaukee        | WI    | 14317 | 08/18/2016 |
| 10000085 | Sydney    | Mcfarland  | P.O. Box 713, 5395 Curabitur Rd.     | Erie             | PA    | 10270 | 08/12/2016 |
| 10000086 | Colin     | Knowles    | P.O. Box 301, 8591 Ipsum. Rd.        | Phoenix          | AZ    | 85323 | 08/15/2016 |
| 10000087 | Zeph      | Horn       | Ap #777-4310 Posuere St.             | Seattle          | WA    | 97337 | 08/15/2016 |
| 10000088 | Mira      | Everett    | P.O. Box 260, 8799 Enim Road         | Minneapolis      | MN    | 98557 | 08/29/2016 |
| 10000089 | Morgan    | Carson     | P.O. Box 268, 3588 Donec Ave         | Augusta          | ME    | 45039 | 08/10/2016 |
| 10000090 | Madonna   | Ayala      | 6759 Id, Rd.                         | Tulsa            | OK    | 56132 | 08/13/2016 |
| 10000091 | Darrel    | Mckay      | P.O. Box 102, 9923 Rutrum St.        | Rock Springs     | WY    | 69328 | 08/26/2016 |
| 10000092 | Portia    | Holcomb    | P.O. Box 245, 847 Sociosqu Street    | Stamford         | CT    | 92697 | 08/13/2016 |
| 10000093 | Uriah     | Higgins    | P.O. Box 469, 2662 Nonummy. Avenue   | Boise            | ID    | 57009 | 08/18/2016 |
| 10000094 | Jana      | Harvey     | 5773 Sagittis Road                   | Lawton           | OK    | 46295 | 08/28/2016 |
| 10000095 | Lionel    | Vazquez    | Ap #485-6869 Vitae St.               | Olathe           | KS    | 67639 | 08/16/2016 |
| 10000096 | Teegan    | Mcleod     | 140-2047 Diam. Av.                   | Wilmington       | DE    | 62889 | 08/31/2016 |
| 10000097 | Baxter    | Casey      | 484-9030 Duis Rd.                    | Nashville        | TN    | 79064 | 08/20/2016 |
| 10000098 | Asher     | Key        | P.O. Box 272, 2016 Dictum. Road      | Casper           | WY    | 34420 | 08/29/2016 |
| 10000099 | Ursa      | Marsh      | P.O. Box 766, 9065 Orci, St.         | Overland Park    | KS    | 71410 | 08/20/2016 |
+----------+-----------+------------+--------------------------------------+------------------+-------+-------+------------+
100 rows in set (0.00 sec)

mysql> 
</code></pre>




## 9. Your company is being acquired by another company. To prepare for this acquisition, update the customer records to guarantee there will be no duplicate IDs with their existing customer IDs


## 10. Your boss needs specialized reports using the billing data and is constantly asking for help to write SQL queries. Create a database view in the metastore so that your boss has customer and billing data joined

### solution

<pre><code>
CREATE EXTERNAL TABLE problem10.solution(
id int, 
fname string, 
lname string, 
city string, 
state string, 
charge double, 
billdate string
)
ROW FORMAT SERDE 
'org.apache.hadoop.hive.serde2.lazy.LazySimpleSerDe' 
WITH SERDEPROPERTIES ( 
'field.delim'='\t', 
'serialization.format'='\t') 
STORED AS INPUTFORMAT 
'org.apache.hadoop.mapred.TextInputFormat' 
OUTPUTFORMAT 
'org.apache.hadoop.hive.ql.io.HiveIgnoreKeyTextOutputFormat'
LOCATION
'hdfs://localhost:8020/user/training/problem10/solution'
;


insert overwrite table problem10.solution
select a.id, a.fname, a.lname, a.city, a.state, b.charge, substr(b.tstamp, 1, 10) as billdata from
problem10.customer a
inner join problem10.billing b
where a.id = b.id
;

</code></pre>

### capture
![Screenshot](https://github.com/recrom/data_ingest_exercise/blob/master/img/problem10.PNG?raw=true)


## 11. Several analysis questions are described below and you will need to write the SQL code to answer them. You can use whichever tool you prefer – Impala or Hive – using whichever method you like best, including shell, script, or the Hue Query Editor, to run your queries.


