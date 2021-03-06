FROM debian:stable

ENV TMP /tmp
WORKDIR $TMP

RUN echo 'deb http://deb.debian.org/debian stable main contrib non-free\n\
deb http://deb.debian.org/debian stable-updates main contrib non-free\n\
deb http://security.debian.org/debian-security stable/updates main contrib non-free\n'\
> /etc/apt/sources.list

# install apt dependencies
RUN apt-get update && \
    apt-get install -y --no-install-recommends vim curl build-essential zlib1g-dev openjdk-8-jre mysql-client \
    syslog-ng libssl-dev && service syslog-ng start && \
    rm -rf /var/lib/apt/lists/*

# install spark
ENV SPARK_VERSION spark-2.3.0-bin-hadoop2.7
ENV SPARK_TAR $SPARK_VERSION.tgz
ENV SPARK_HOME /usr/local/spark

RUN curl -L http://mirror.nbtelecom.com.br/apache/spark/spark-2.3.0/$SPARK_TAR > ./$SPARK_TAR && \
    tar -xzvf $SPARK_TAR && rm $SPARK_TAR && mv $SPARK_VERSION $SPARK_HOME

# install spark jars
ENV SPARK_JARS $SPARK_HOME/jars
WORKDIR $SPARK_JARS

ENV MAVEN_URL http://central.maven.org/maven2/
ENV AWS_JAVA_SDK_PATH com/amazonaws/aws-java-sdk/1.7.4/
ENV ELASTICSEARCH_HADOOP_PATH org/elasticsearch/elasticsearch-hadoop/6.0.0/
ENV HADOOP_AWS_PATH org/apache/hadoop/hadoop-aws/2.7.2/
ENV JODA_TIME_PATH joda-time/joda-time/2.9.9/
ENV MYSQL_CONNECTOR_JAVA_PATH mysql/mysql-connector-java/5.1.45/

ENV AWS_JAVA_SDK_JAR aws-java-sdk-1.7.4.jar
ENV ELASTICSEARCH_HADOOP_JAR elasticsearch-hadoop-6.0.0.jar
ENV HADOOP_AWS_JAR hadoop-aws-2.7.2.jar
ENV JODA_TIME_JAR joda-time-2.9.9.jar
ENV MYSQL_CONNECTOR_JAVA_JAR mysql-connector-java-5.1.45.jar

RUN curl -L $MAVEN_URL$AWS_JAVA_SDK_PATH$AWS_JAVA_SDK_JAR > ./$AWS_JAVA_SDK_JAR && \
    curl -L $MAVEN_URL$ELASTICSEARCH_HADOOP_PATH$ELASTICSEARCH_HADOOP_JAR > ./$ELASTICSEARCH_HADOOP_JAR && \
    curl -L $MAVEN_URL$HADOOP_AWS_PATH$HADOOP_AWS_JAR > ./$HADOOP_AWS_JAR && \
    curl -L $MAVEN_URL$JODA_TIME_PATH$JODA_TIME_JAR > ./$JODA_TIME_JAR && \
    curl -L $MAVEN_URL$MYSQL_CONNECTOR_JAVA_PATH$MYSQL_CONNECTOR_JAVA_JAR > ./$MYSQL_CONNECTOR_JAVA_JAR && \
    chmod 555 $AWS_JAVA_SDK_JAR $ELASTICSEARCH_HADOOP_JAR $HADOOP_AWS_JAR $JODA_TIME_JAR $MYSQL_CONNECTOR_JAVA_JAR

# install python
ENV PYTHON_VERSION 3.6.5
ENV PYTHON_FOLDER Python-$PYTHON_VERSION
ENV PYTHON_TAR $PYTHON_FOLDER.tgz

RUN curl -L https://www.python.org/ftp/python/$PYTHON_VERSION/$PYTHON_TAR > ./$PYTHON_TAR && \
    tar -xzvf $PYTHON_TAR && cd Python-3.6.5 && \
    ./configure --enable-optimizations --with-ensurepip=install && make -j8 build_all && make -j8 altinstall && \
    update-alternatives --install /usr/bin/python python /usr/local/bin/python3.6 50 && \
    update-alternatives --install /usr/bin/pip pip /usr/local/bin/pip3.6 50 && \
    cd .. && rm -rf $PYTHON_FOLDER $PYTHON_TAR

WORKDIR /

# remove build dependencies
RUN apt-get remove -y curl vim

# overwrite paths
ENV PATH $PATH:$SPARK_HOME/bin
ENV PYTHONPATH=$PYTHONPATH:\
/usr/local/spark/python/:\
/usr/local/spark/python/lib/:\
/usr/local/spark/python/lib/py4j-0.10.6-src.zip

CMD bash -c "tail -f /var/log/syslog"
