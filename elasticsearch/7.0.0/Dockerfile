FROM centos:7
MAINTAINER zhangyu

ENV PATH /usr/share/elasticsearch/bin:$PATH

WORKDIR /usr/share/elasticsearch



COPY C.utf8 /usr/lib/locale/C.utf8
ENV LANG C.UTF-8
ENV LC_ALL C.UTF-8
##set timezone
ENV TZ=Asia/Shanghai
RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone

 

RUN  yum -y install kde-l10n-Chinese && yum -y reinstall glibc-common  

RUN yum install -y nc wget libuuid && yum update -y && yum clean all && rm -rf /var/cache/yum

 
RUN groupadd -g 9200 elasticsearch && \
    adduser -u 9200 -g 9200 -G 0 -d /usr/share/elasticsearch elasticsearch && \
    chmod 0775 /usr/share/elasticsearch && \
    chgrp 0 /usr/share/elasticsearch 

###事先下载好--推荐
###COPY elasticsearch-7.0.0-linux-x86_64.tar.gz /opt/ 
##7.0.0版本自带了jdk


ENV ES_VERSION 7.0.0

RUN cd /opt && wget http://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-${ES_VERSION}-linux-x86_64.tar.gz 

RUN cd /usr/share/elasticsearch &&  tar zxf /opt/elasticsearch-${ES_VERSION}-linux-x86_64.tar.gz --strip-components=1

RUN grep ES_DISTRIBUTION_TYPE=tar /usr/share/elasticsearch/bin/elasticsearch-env \
    && sed -ie 's/ES_DISTRIBUTION_TYPE=tar/ES_DISTRIBUTION_TYPE=docker/' /usr/share/elasticsearch/bin/elasticsearch-env

##增加自定义路径/data/elasticsearch/
RUN mkdir -p /usr/share/elasticsearch/data && mkdir -p /data/elasticsearch/{data,logs}
RUN chmod -R 0775 /usr/share/elasticsearch/{config,data,logs}

RUN ln -sf /etc/pki/ca-trust/extracted/java/cacerts /usr/share/elasticsearch/jdk/lib/security/cacerts


COPY  elasticsearch.yml  log4j2.properties /usr/share/elasticsearch/config/


COPY docker-entrypoint.sh /usr/local/bin/docker-entrypoint.sh

# Openshift overrides USER and uses ones with randomly uid>1024 and gid=0
# Allow ENTRYPOINT (and ES) to run even with a different user

RUN chown 9200:0 /usr/local/bin/docker-entrypoint.sh && \
    chmod g=u /etc/passwd && \
    chmod 0775 /usr/local/bin/docker-entrypoint.sh && \
    chown -R 9200:0 /usr/share/elasticsearch && \
    chown -R 9200:0 /data/elasticsearch && \
    chmod 777 /tmp && \ 
    rm -rf /opt/elasticsearch-${ES_VERSION}-linux-x86_64.tar.gz 
    
EXPOSE 9200 9300


ENTRYPOINT ["/usr/local/bin/docker-entrypoint.sh"]


CMD ["eswrapper"]

###docker build -f Dockerfile -t="elasticsearch:7.0.0" .
