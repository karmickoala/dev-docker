FROM centos:centos7

RUN yum -y install wget && yum -y clean all
RUN yum -y install unzip && yum -y clean all

RUN mkdir /opt/java
RUN wget -O - --no-check-certificate --no-cookies --header "Cookie: oraclelicense=accept-securebackup-cookie" http://download.oracle.com/otn-pub/java/jdk/8u144-b01/090f390dda5b47b9b721c7dfaa008135/jdk-8u144-linux-x64.tar.gz | tar zxf - -C /opt/java && ln -s /opt/java/jdk1.8.0_144 /opt/java/current

RUN mkdir /opt/solr
RUN wget -O - http://archive.apache.org/dist/lucene/solr/6.5.1/solr-6.5.1.tgz | tar zxf - -C /opt/solr && ln -s /opt/solr/solr-6.5.1 /opt/solr/current

RUN chown -R root:root /opt/solr/solr-6.5.1

