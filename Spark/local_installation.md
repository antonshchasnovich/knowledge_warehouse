[<-----](https://github.com/s1tcomsfan/knowledge_warehouse/blob/main/Spark/contents.md)

## Local installation steps

1) Downloading Apache Spark
    * go to [Spark Downloading Page](https://spark.apache.org/downloads.html)
    * select “Pre-built for Apache Hadoop 2.7” from the drop-down menu
    * click the “Download Spark” link

2) Extract the tarball content

3) cd into that directory

4) to start PySpark, cd to the bin directory and type pyspark

```
$ pyspark
Python 3.7.3 (default, Mar 27 2019, 09:23:15)
[Clang 10.0.1 (clang-1001.0.46.3)] on darwin
Type "help", "copyright", "credits" or "license" for more information.
20/02/16 19:28:48 WARN NativeCodeLoader: Unable to load native-hadoop library
for your platform... using builtin-java classes where applicable
Welcome to
 ____ __
 / __/__ ___ _____/ /__
 _\ \/ _ \/ _ `/ __/ '_/
 /__ / .__/\_,_/_/ /_/\_\ version 3.0.0-preview2
 /_/
Using Python version 3.7.3 (default, Mar 27 2019 09:23:15)
SparkSession available as 'spark'.
>>> spark.version
'3.0.0-preview2'
>>>
```

**There are possible errors on step 4**

***

**Error:** RuntimeError: The current Numpy installation fails to pass a sanity check due to a bug in the windows runtime

**Solution:** The temporary solution is to use numpy 1.19.3
```
pip install numpy==1.19.3
```

***

**Error:** Could not locate executable null\bin\winutils.exe in the Hadoop binaries

**Solution:** 
* Download [winutils.exe](http://public-repo-1.hortonworks.com/hdp-win-alpha/winutils.exe)
* Create folder, say C:\winutils\bin
* Copy winutils.exe inside C:\winutils\bin
* Set environment variable HADOOP_HOME to C:\winutils
* rerun cmd

[<-----](https://github.com/s1tcomsfan/knowledge_warehouse/blob/main/Spark/contents.md)

***

**Error:** Error while deleting tmp files
```
ERROR ShutdownHookManager: Exception while deleting Spark temp dir: path/to/tmp/dir ...
WARN SparkEnv: Exception while deleting Spark temp dir: path/to/tmp/dir ...
```

**Solution:**
***Can't make a normal colution so there few steps to hide logs and make deleting of tmp files more comfortable:***
* Change Spark local tmp dir by setting of enviroment variable SPARK_LOCAL_DIRS -> d:\spark-tmp\tmp 
* Add to .../root/dir/conf/log4j.properties file (if file is not exist copy and rename log4j.properties.template):
```
log4j.logger.org.apache.spark.util.ShutdownHookManager=OFF
log4j.logger.org.apache.spark.SparkEnv=ERROR
``` 
* spark-shell internally calls spark-shell.cmd file. So add rmdir /q /s "your_dir\tmp" (work with using of spark-shell - scala API)
* delete tmp dir manually

***

5) Spark graphical user interface (UI) in local mode is available at address **http://localhost:4040/**