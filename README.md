# FLiPS-Xavier-Sensor
MQTT, Apache Pulsar, Apache Flink, Apache Spark, Pulsar SQL


### Run

````

Using Spark's default log4j profile: org/apache/spark/log4j-defaults.properties
Setting default log level to "WARN".
To adjust logging level use sc.setLogLevel(newLevel). For SparkR, use setLogLevel(newLevel).
Spark context Web UI available at http://pulsar1.fios-router.home:4040
Spark context available as 'sc' (master = spark://pulsar1:7077, app id = app-20220204140604-0000).
Spark session available as 'spark'.
Welcome to
      ____              __
     / __/__  ___ _____/ /__
    _\ \/ _ \/ _ `/ __/  '_/
   /___/ .__/\_,_/_/ /_/\_\   version 3.2.0
      /_/
         
Using Scala version 2.12.15 (OpenJDK 64-Bit Server VM, Java 1.8.0_312)
Type in expressions to have them evaluated.
Type :help for more information.

scala> val dfPulsar = spark.readStream.format("pulsar").option("service.url", "pulsar://localhost:6650").option("admin.url", "http://localhost:8080").option("topic", "persistent://public/default/iotjetsonjson").load()
dfPulsar: org.apache.spark.sql.DataFrame = [camera: string, cpu: double ... 25 more fields]

scala> dfPulsar.printSchema()
root
 |-- camera: string (nullable = true)
 |-- cpu: double (nullable = false)
 |-- cputemp: string (nullable = true)
 |-- cputempf: string (nullable = true)
 |-- diskusage: string (nullable = true)
 |-- filename: string (nullable = true)
 |-- gputemp: string (nullable = true)
 |-- gputempf: string (nullable = true)
 |-- host: string (nullable = true)
 |-- host_name: string (nullable = true)
 |-- imageinput: string (nullable = true)
 |-- ipaddress: string (nullable = true)
 |-- macaddress: string (nullable = true)
 |-- memory: double (nullable = false)
 |-- networktime: double (nullable = false)
 |-- runtime: string (nullable = true)
 |-- systemtime: string (nullable = true)
 |-- te: string (nullable = true)
 |-- top1: string (nullable = true)
 |-- top1pct: double (nullable = false)
 |-- uuid: string (nullable = true)
 |-- __key: binary (nullable = true)
 |-- __topic: string (nullable = true)
 |-- __messageId: binary (nullable = true)
 |-- __publishTime: timestamp (nullable = true)
 |-- __eventTime: timestamp (nullable = true)
 |-- __messageProperties: map (nullable = true)
 |    |-- key: string
 |    |-- value: string (valueContainsNull = true)


scala> 

scala> val pQuery = dfPulsar.selectExpr("*").writeStream.format("console").option("truncate", "false").start()
22/02/04 14:13:56 WARN ResolveWriteToStream: Temporary checkpoint location created which is deleted normally when the query didn't fail: /tmp/temporary-a8945166-0706-41ad-a141-879ebe39eb51. If it's required to delete it under any circumstances, please set spark.sql.streaming.forceDeleteTempCheckpointLocation to true. Important to know deleting temp checkpoint folder is best effort.
22/02/04 14:13:56 WARN ResolveWriteToStream: spark.sql.adaptive.enabled is not supported in streaming DataFrames/Datasets and will be disabled.
pQuery: org.apache.spark.sql.streaming.StreamingQuery = org.apache.spark.sql.execution.streaming.StreamingQueryWrapper@4e06d526

scala> 22/02/04 14:13:58 WARN package: Truncated the string representation of a plan since it was too large. This behavior can be adjusted by setting 'spark.sql.debug.maxToStringFields'.
-------------------------------------------                                     
Batch: 0
-------------------------------------------
+------+---+-------+--------+---------+--------+-------+--------+----+---------+----------+---------+----------+------+-----------+-------+----------+---+----+-------+----+-----+-------+-----------+-------------+-----------+-------------------+
|camera|cpu|cputemp|cputempf|diskusage|filename|gputemp|gputempf|host|host_name|imageinput|ipaddress|macaddress|memory|networktime|runtime|systemtime|te |top1|top1pct|uuid|__key|__topic|__messageId|__publishTime|__eventTime|__messageProperties|
+------+---+-------+--------+---------+--------+-------+--------+----+---------+----------+---------+----------+------+-----------+-------+----------+---+----+-------+----+-----+-------+-----------+-------------+-----------+-------------------+
+------+---+-------+--------+---------+--------+-------+--------+----+---------+----------+---------+----------+------+-----------+-------+----------+---+----+-------+----+-----+-------+-----------+-------------+-----------+-------------------+

-------------------------------------------                                     
Batch: 1
-------------------------------------------
+-----------+----+-------+--------+----------+----------------------------------------------------------+-------+--------+--------------+--------------+----------------------------------------------------------+-------------+-----------------+------+------------------+-------+-------------------+----------------+-------------+-------------+----------------------------------+-------------------------------------------------------------------------------------------------------+-----------------------------------------+-------------------------+-----------------------+-----------+-------------------+
|camera     |cpu |cputemp|cputempf|diskusage |filename                                                  |gputemp|gputempf|host          |host_name     |imageinput                                                |ipaddress    |macaddress       |memory|networktime       |runtime|systemtime         |te              |top1         |top1pct      |uuid                              |__key                                                                                                  |__topic                                  |__messageId              |__publishTime          |__eventTime|__messageProperties|
+-----------+----+-------+--------+----------+----------------------------------------------------------+-------+--------+--------------+--------------+----------------------------------------------------------+-------------+-----------------+------+------------------+-------+-------------------+----------------+-------------+-------------+----------------------------------+-------------------------------------------------------------------------------------------------------+-----------------------------------------+-------------------------+-----------------------+-----------+-------------------+
|/dev/video0|37.2|29.5   |85      |24512.6 MB|/home/nvidia/nvme/images/out_video0_fax_20220204191405.jpg|30.0   |86      |nvidia-desktop|nvidia-desktop|/home/nvidia/nvme/images/img_video0_cok_20220204191405.jpg|192.168.1.228|70:66:55:15:b4:a5|77.9  |25.066272735595703|6      |02/04/2022 14:14:10|5.91699481010437|window screen|26.8310546875|xav_uuid_video0_jfh_20220204191405|[78 61 76 5F 75 75 69 64 5F 76 69 64 65 6F 30 5F 6A 66 68 5F 32 30 32 32 30 32 30 34 31 39 31 34 30 35]|persistent://public/default/iotjetsonjson|[08 D1 A0 05 10 00 20 00]|2022-02-04 14:14:16.517|null       |{}                 |
+-----------+----+-------+--------+----------+----------------------------------------------------------+-------+--------+--------------+--------------+----------------------------------------------------------+-------------+-----------------+------+------------------+-------+-------------------+----------------+-------------+-------------+----------------------------------+-------------------------------------------------------------------------------------------------------+-----------------------------------------+-------------------------+-----------------------+-----------+-------------------+

CREATE TABLE iotjetsonjson
(
  `id` STRING, uuid STRING, ir STRING,
  `end` STRING, lux STRING, gputemp STRING, 
  cputemp STRING, `te` STRING, systemtime STRING, hum STRING,
 memory STRING, gas STRING, pressure STRING, 
 `host` STRING, diskusage STRING, ipaddress STRING, macaddress STRING, 
  gputempf STRING, host_name STRING, camera STRING, filename STRING, 
    `runtime` STRING, cpu STRING,cputempf STRING, imageinput STRING,
    `networktime` STRING, top1 STRING, top1pct STRING, 
  publishTime TIMESTAMP(3) METADATA,
  WATERMARK FOR publishTime AS publishTime - INTERVAL '5' SECOND
) WITH (
  'connector' = 'pulsar',
  'topic' = 'persistent://public/default/iotjetsonjson',
  'value.format' = 'json',
  'scan.startup.mode' = 'earliest',
  'service-url' = 'pulsar://pulsar1:6650',
  'admin-url' = 'http://pulsar1:8080'
);


bin/pulsar sql

show tables in pulsar."public/default";

select * from pulsar."public/default".iotjetsonjson;

select * from pulsar."public/default".iotjetsonjson order by systemtime desc;

````

### Web

* Spark URL http://pulsar1.fios-router.home:4040/stages/
* FLink URL
* Pulsar SQL URL http://pulsar1:8081/ui/
* Pulsar Manager URL http://pulsar1:9527/#/login?redirect=%2F

### Code

* Spark -> https://github.com/tspannhw/FLiPS-SparkOnPulsar/blob/main/README.md
* Flink -> https://github.com/tspannhw/pulsar-flinksql-1.13.2
* Trino/Presto SQL/Pulsar SQL -> https://github.com/tspannhw/FLiP-Into-Trino

