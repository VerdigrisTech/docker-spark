FROM openjdk:8u171-jdk-slim-stretch
WORKDIR /tmp

ARG SPARK_VERSION
ENV SPARK_VERSION=${SPARK_VERSION}
ENV SPARK_BINARY_TARBALL spark-${SPARK_VERSION}-bin-hadoop2.7.tgz
ENV SPARK_CASSANDRA_CONNECTOR_VERSION 2.3.1
ENV PYSPARK_CASSANDRA_JAR_VERSION 0.9.0
ENV POSTGRESQL_DRIVER_VERSION 42.2.4

COPY mirrors /tmp/
COPY KEYS /tmp/
COPY ${SPARK_BINARY_TARBALL}.asc /tmp/
COPY ${SPARK_BINARY_TARBALL}.sha512 /tmp/

RUN apt-get update \
  && apt-get install -y curl gnupg python python2.7 python-pip python3-pip \
  && export MIRROR=$(shuf -n 1 mirrors)/spark/spark-$SPARK_VERSION/spark-$SPARK_VERSION-bin-hadoop2.7.tgz \
  && echo "Downloading from ${MIRROR}" \
  && curl -#O ${MIRROR} \
  && gpg --import KEYS \
  && gpg --verify ${SPARK_BINARY_TARBALL}.asc ${SPARK_BINARY_TARBALL} \
  && gpg --print-md SHA512 ${SPARK_BINARY_TARBALL} > /tmp/${SPARK_BINARY_TARBALL}.sha512 \
  && cmp /tmp/${SPARK_BINARY_TARBALL}.sha512 ${SPARK_BINARY_TARBALL}.sha512 \
  && tar xvzf ${SPARK_BINARY_TARBALL} --exclude=**/data --exclude=**/examples --exclude=**/kubernetes --exclude=**/R --exclude=*.cmd -C /opt \
  && ln -s /opt/spark-${SPARK_VERSION}-bin-hadoop2.7 /opt/spark \
  && cd /opt/spark/jars \
  && export SPARK_CASSANDRA_CONNECTOR_JAR_URL=http://central.maven.org/maven2/com/datastax/spark/spark-cassandra-connector_2.11/${SPARK_CASSANDRA_CONNECTOR_VERSION}/spark-cassandra-connector_2.11-${SPARK_CASSANDRA_CONNECTOR_VERSION}.jar \
  && echo "Downloading from ${SPARK_CASSANDRA_CONNECTOR_JAR_URL}" \
  && curl -#O ${SPARK_CASSANDRA_CONNECTOR_JAR_URL} \
  && export PYSPARK_CASSANDRA_JAR_URL=https://dl.bintray.com/spark-packages/maven/anguenot/pyspark-cassandra/${PYSPARK_CASSANDRA_JAR_VERSION}/pyspark-cassandra-${PYSPARK_CASSANDRA_JAR_VERSION}.jar \
  && echo "Downloading from ${PYSPARK_CASSANDRA_JAR_URL}" \
  && curl -#O ${PYSPARK_CASSANDRA_JAR_URL} \
  && export POSTGRESQL_DRIVER_JAR_URL=http://central.maven.org/maven2/org/postgresql/postgresql/${POSTGRESQL_DRIVER_VERSION}/postgresql-${POSTGRESQL_DRIVER_VERSION}.jar \
  && echo "Downloading from ${POSTGRESQL_DRIVER_JAR_URL}" \
  && curl -#O ${POSTGRESQL_DRIVER_JAR_URL} \
  && rm /tmp/${SPARK_BINARY_TARBALL}* \
  && apt-get purge --auto-remove -y curl gnupg \
  && apt-get autoclean

RUN export PATH=$PATH:/opt/spark/bin

ENV PY4J_VERSION 0.10.7
ENV SPARK_HOME /opt/spark
ENV PYTHONPATH ${SPARK_HOME}/python:${SPARK_HOME}/python/lib/py4j-${PY4J_VERSION}-src.zip:$PYTHONPATH
ENV PATH $PATH:/opt/spark/bin:/opt/spark/sbin

WORKDIR /app
