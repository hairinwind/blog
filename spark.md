# spark

## install and run spark on windows
download the tar.gz and unzip it 
download winutils and put it in the bin directory, https://github.com/steveloughran/winutils/blob/master/hadoop-2.7.1/bin/winutils.exe 
set up the environemnt parameter SPARK_HOME to the spark unzip directory
```
setx SPARK_HOME C:\spark-2.1.0-bin-hadoop2.7  
```
restart command window
run spark-shell


https://medium.com/@GalarnykMichael/install-spark-on-windows-pyspark-4498a5d8d66c
https://www.iwwenbo.com/run-apache-spark-on-windows/

## Resilient Distributed Datasets (RDDs)
RDD is a fault-tolerant collection of elements that can be operated on in parallel.
There are two ways to create RDDs: 
- parallelizing an existing collection in your driver program
- referencing a dataset in an external storage system, such as a shared filesystem, HDFS, HBase, or any data source offering a Hadoop InputFormat.

RDDs support two types of operations: 
- transformations, which create a new dataset from an existing one, 
- actions, which return a value to the driver program after running a computation on the dataset.

All transformations in Spark are lazy. The transformations are only computed when an **action** requires a result to be returned to the driver program.

In memory RDD can be persisted by _persist_ or _cache_

## DataFrames
DataFrames are more efficient than RDD’s in many use cases.

## Datasets
Datasets are the new DataFrames. In fact, they are an extension of the DataFrame API. The API is familiar making it very easy to use and the list of benefits in terms of performance is huge. Datasets have a simpler API, execute faster and have a lower memory overhead in comparison to RDD’s.

## Shared Variables
### Broadcast Variables
Broadcast variables allow the programmer to keep a read-only variable cached on each machine rather than shipping a copy of it with tasks. They can be used, for example, to give every node a copy of a large input dataset in an efficient manner.

### accumulators
