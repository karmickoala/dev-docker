FROM centos:centos7

RUN yum -y install wget && yum -y clean all
RUN yum -y install unzip && yum -y clean all

RUN mkdir /opt/java
RUN wget -O - --no-check-certificate --no-cookies --header "Cookie: oraclelicense=accept-securebackup-cookie" http://download.oracle.com/otn-pub/java/jdk/8u144-b01/090f390dda5b47b9b721c7dfaa008135/jdk-8u144-linux-x64.tar.gz | tar zxf - -C /opt/java && ln -s /opt/java/jdk1.8.0_144 /opt/java/current

RUN mkdir /opt/zookeeper
RUN wget -O - http://ftp.jaist.ac.jp/pub/apache/zookeeper/zookeeper-3.4.6/zookeeper-3.4.6.tar.gz | tar zxf - -C /opt/zookeeper && ln -s /opt/zookeeper/zookeeper-3.4.6 /opt/zookeeper/current

RUN chown -R root:root /opt/zookeeper/zookeeper-3.4.6

RUN mkdir /opt/zookeeper/data
RUN mkdir /opt/zookeeper/log

