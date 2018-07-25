FROM openjdk:8u171-jdk-slim-stretch
WORKDIR /tmp

ARG SPARK_VERSION
ENV SPARK_VERSION=${SPARK_VERSION}
ENV SPARK_BINARY_TARBALL spark-${SPARK_VERSION}-bin-hadoop2.7.tgz
ENV SPARK_CASSANDRA_CONNECTOR_VERSION 2.3.1
ENV PYTHON_VERSION 3.6.6
ENV PYTHON_SOURCE_TARBALL Python-${PYTHON_VERSION}.tar.xz
ENV PYSPARK_CASSANDRA_JAR_VERSION 0.9.0
ENV POSTGRESQL_DRIVER_VERSION 42.2.4

COPY mirrors /tmp/
COPY KEYS /tmp/
COPY PYTHONKEYS /tmp/
COPY ${SPARK_BINARY_TARBALL}.asc /tmp/
COPY ${SPARK_BINARY_TARBALL}.sha512 /tmp/
COPY ${PYTHON_SOURCE_TARBALL}.asc /tmp/

# Build and install Python 2.7 & 3.6 (Debian Stretch does not have 3.6 packages)
RUN apt-get update \
  && apt-get install -y build-essential curl gnupg libbz2-dev libdb5.3-dev \
    libexpat1-dev libgdbm-dev liblzma-dev libncurses5-dev libncursesw5-dev \
    libreadline-dev libsqlite3-dev libssl-dev python2.7 python-pip tk-dev \
    zlib1g-dev \
  && export PYTHON_URL=https://www.python.org/ftp/python/${PYTHON_VERSION}/Python-${PYTHON_VERSION}.tar.xz \
  && echo "Downloading from ${PYTHON_URL}" \
  && curl -#O ${PYTHON_URL} \
  && gpg --import PYTHONKEYS \
  && gpg --verify ${PYTHON_SOURCE_TARBALL}.asc ${PYTHON_SOURCE_TARBALL} \
  && tar xvf ${PYTHON_SOURCE_TARBALL} \
  && cd Python-${PYTHON_VERSION}/ \
  && ./configure --enable-optimizations \
  && make -j $(($(nproc --all) + 1)) \
  && make install \
  && apt-get purge --auto-remove -y build-essential libbz2-dev libdb5.3-dev \
    libexpat1-dev libgdbm-dev liblzma-dev libncurses5-dev libncursesw5-dev \
    libreadline-dev libsqlite3-dev libssl-dev tk-dev zlib1g-dev \
  && apt-get autoclean \
  && cd ../ \
  && rm -rf /tmp/Python-${PYTHON_VERSION}/ \
  && rm /tmp/${PYTHON_SOURCE_TARBALL}* \
  && rm /tmp/PYTHONKEYS

# Download Spark and dependencies
RUN export MIRROR=$(shuf -n 1 mirrors)/spark/spark-$SPARK_VERSION/spark-$SPARK_VERSION-bin-hadoop2.7.tgz \
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
  && rm /tmp/${SPARK_BINARY_TARBALL}*

RUN export PATH=$PATH:/opt/spark/bin

ENV PY4J_VERSION 0.10.7
ENV SPARK_HOME /opt/spark
ENV PYTHONPATH ${SPARK_HOME}/python:${SPARK_HOME}/python/lib/py4j-${PY4J_VERSION}-src.zip:${SPARK_HOME}/jars/pyspark-cassandra-${PYSPARK_CASSANDRA_JAR_VERSION}.jar:$PYTHONPATH
ENV PATH $PATH:/opt/spark/bin:/opt/spark/sbin

WORKDIR /app
