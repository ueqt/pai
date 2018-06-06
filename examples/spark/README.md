<!--
  Copyright (c) Microsoft Corporation
  All rights reserved.

  MIT License

  Permission is hereby granted, free of charge, to any person obtaining a copy of this software and associated
  documentation files (the "Software"), to deal in the Software without restriction, including without limitation
  the rights to use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies of the Software, and
  to permit persons to whom the Software is furnished to do so, subject to the following conditions:
  The above copyright notice and this permission notice shall be included in all copies or substantial portions of the Software.

  THE SOFTWARE IS PROVIDED *AS IS*, WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED, INCLUDING
  BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND
  NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM,
  DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
  OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.
-->


# Spark on PAI

This guide introduces how to run [Spark](https://spark.apache.org/) workload on PAI.
The following contents show some basic Spark examples, other customized Spark code can be run similarly.


## Contents

1. [Basic environment](#basic-environment)
2. [Advanced environment](#advanced-environment)
3. [Keras examples](#keras-examples)
4. [Frequently asked questions](#faq)


## Basic environment

First of all, Spark submit job to yarn need hadoop config files.


1. Prepare Spark binary

1.1. Download from [Spark Binary Download](https://spark.apache.org/downloads.html)

1.2. Uncompress

```
tar -zxvf spark-2.3.0-bin-hadoop2.7.tgz
```

2. Prepare hadoop configuration file

After execute:

1.1. PAI Build Hadoop binary

1.2. pai/pai-management execute:

```
./prepare_hadoop_configuration
```

You could get hadoop config files at pai/pai-management/bootstrap/hadoop-service/hadoop-configuration

1.3. rename resourcemanager-yarn-site.xml to yarn-site.xml

1.4. reconfig IP address at:

```
core-site.xml
yarn-site.xml
```

## Advanced environment

You can skip this section if you do not need to prepare other dependencies.

1. jar version problem:

If user user PAI's Hadoop 2.7 and Spark 2.3, then shoud replace Sprak's jar file version.

1.1. Download jar

```
Download jersey-core-1.9.jar and jersey-client-1.9.jar
```

1.2. Replace jar

```
Replace $SPARK_HOME/jars/jersey-client-2.22.2.jar with jersey-core-1.9.jar and jersey-client-1.9.jar.
```

2. Prepare python env:

If user want to submit Spark, PySpark or MMLSpark job. Please view the followings:

You could prepare a python env with anaconda or use other python enviroment. The environment shoud install the depenecnies which will use by PySpark jobs

2.1. Creat a env:

```
conda create -p /home/ubuntu/dev --copy -y -q python=3 pandas scikit-learn
```

By using the --copy, we "Install all packages using copies instead of hard- or soft-linking." The headers in various files in the bin/ directory may have lines like #!/home/ubuntu/dev/bin/python. But we don't need to be concerned about that -- we're not going to be using 2to3, idle, pip, etc. If we zipped up the environment, we could move this onto another machine.

We're very close to our Uber Python jar -- now with a zipped Conda directory in mind, let's proceed.

2.2. zip the file

```
zip -r env.zip dev
```

# Submit Spark Job to PAI examples

1. Spark job

```
export HADOOP_CONF_DIR=/pai/pai-management/bootstrap/hadoop-service/hadoop-configuration
./bin/spark-submit  --class org.apache.spark.examples.SparkPi  --master yarn  --deploy-mode cluster  /spark-2.3.0-bin-hadoop2.7/examples/jars/spark-examples_2.11-2.3.0.jar 10
```

2. PySpark job

```
export HADOOP_CONF_DIR=/hadoop-service/hadoop-configuration
 ./bin/spark-submit --master yarn --deploy-mode cluster  --archives env.zip --conf spark.yarn.appMasterEnv.PYSPARK_PYTHON=envnew.zip/bin/python TestPySpark.py
```

3. MMLSpark job

``` 
export HADOOP_CONF_DIR=/pai/pai-management/bootstrap/hadoop-service/hadoop-configuration
 ./bin/spark-submit --packages Azure:mmlspark:0.12 --master yarn --deploy-mode cluster  --archives env.zip --conf spark.yarn.appMasterEnv.PYSPARK_PYTHON=envnew.zip/bin/python Income.py
```


