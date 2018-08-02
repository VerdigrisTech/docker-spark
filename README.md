# [![docker-spark](https://s3-us-west-2.amazonaws.com/verdigris/spark-docker.svg)](https://hub.docker.com/r/verdigristech/spark)

[![Build Status](https://img.shields.io/circleci/token/f41a8ea8d8fe8a47d6d409f60f53230c8c21ff67/project/VerdigrisTech/docker-spark.svg)](https://circleci.com/gh/VerdigrisTech/docker-spark)
[![Docker Pulls](https://img.shields.io/docker/pulls/verdigristech/spark.svg)](https://hub.docker.com/r/verdigristech/spark/)

Containers that contain prebuilt Apache Spark framework.

## Usage

The simplest way to get started with Spark is to run the image directly as
container. You will need to mount the directory which contains your Spark
application to get started.

### Submitting applications

```console
docker run -v .:/app -it verdigristech/spark:2.3.1 spark-submit --class com.example.app.EntryPointClass MySparkApp.jar
```

This will spin up the container and automatically run your Spark application as
`local[*]` master. Please note that the default working directory for the image
is `/app`.

You may pass in all of the standard `spark-submit` arguments. For more
information, refer to the official Spark documentation on
[Submitting Applications](https://spark.apache.org/docs/latest/submitting-applications.html).

### Pyspark

This image comes prebundled with Python 2.7 and 3.6. Submitting your Pyspark
job similar to the default example above. You will need to change the
application JAR file to a Python file that contains the entry point of your
application.

```console
docker run -v .:/app -it verdigristech/spark:2.3.1 spark-submit my_spark_app.py
```

#### Running Pyspark REPL

To run a Pyspark REPL console, run the `pyspark` command:

```console
$ docker run --rm -it verdigristech/spark:2.3.1 pyspark
Python 2.7.13 (default, Nov 24 2017, 17:33:09)
[GCC 6.3.0 20170516] on linux2
Type "help", "copyright", "credits" or "license" for more information.
2018-07-26 20:51:58 WARN  NativeCodeLoader:62 - Unable to load native-hadoop library for your platform... using builtin-java classes where applicable
Setting default log level to "WARN".
To adjust logging level use sc.setLogLevel(newLevel). For SparkR, use setLogLevel(newLevel).
Welcome to
      ____              __
     / __/__  ___ _____/ /__
    _\ \/ _ \/ _ `/ __/  '_/
   /__ / .__/\_,_/_/ /_/\_\   version 2.3.1
      /_/

Using Python version 2.7.13 (default, Nov 24 2017 17:33:09)
SparkSession available as 'spark'.
>>>
```

#### Setting Python version

By default, Pyspark uses Python 2.7. You may override the default version by
changing `PYSPARK_PYTHON` environment variable to `/usr/local/bin/python3.6`:

```console
docker run --rm -e "PYSPARK_PYTHON=/usr/local/bin/python3.6" -it verdigristech/spark:2.3.1 pyspark
```

-----

© 2018 [Verdigris Technologies, Inc](https://verdigris.co). All rights reserved.

<sub>
Verdigris, Verdigris Technologies, VerdigrisTech, and the Verdigris logo are
registered trademarks or trademarks of Verdigris Technologies, Inc. in the
United States and other countries. Apache Spark, Spark, Apache, the Apache
feather logo, and the Apache Spark project logo are either registered
trademarks or trademarks of The Apache Software Foundation in the United States
and other countries. Docker, the Docker “Moby Dock” whale logo, Dockerfile are
registered trademarks of Docker, Inc. in the United States and other countries.
All other marks mentioned may be trademarks or registered trademarks of their
respective owners.
</sub>
