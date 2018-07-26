# ![docker-spark](https://s3-us-west-2.amazonaws.com/verdigris/spark-docker.svg)

Containers that contain prebuilt Apache Spark framework.

## Usage

The simplest way to get started with Spark is to run the image directly as
container. You will need to mount the directory which contains your Spark
application to get started.

```console
docker run -v .:/app -it verdigristech/spark:2.3.1 spark-submit MySparkApp.jar
```
