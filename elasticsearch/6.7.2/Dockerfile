FROM centos:7

ENV PATH /usr/share/elasticsearch/bin:$PATH



COPY C.utf8 /usr/lib/locale/C.utf8
ENV LANG C.UTF-8
ENV LC_ALL C.UTF-8
##set timezone
ENV TZ=Asia/Shanghai
RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone

 

###下载太慢
##ENV JAVA_HOME /opt/jdk-12
###RUN curl --retry 8 -s https://download.java.net/java/GA/jdk12/GPL/openjdk-12_linux-x64_bin.tar.gz | tar -C /opt -zxf -  && rm -rf openjdk-12_linux-x64_bin.tar.gz
    

####RUN ln -sf /etc/pki/ca-trust/extracted/java/cacerts /opt/jdk-12/lib/security/cacerts

##用yum安装
RUN  yum install -y unzip which nc epel-release  wget which kde-l10n-Chinese && \
     rpm --import /etc/pki/rpm-gpg/RPM-GPG-KEY-EPEL-7 && \
     yum -y reinstall glibc-common &&\
     yum -y --enablerepo=epel-testing install java-12-openjdk && \
     yum update -y && \
     yum clean all &&\
     rm -rf /var/cache/yum
     
ENV JAVA_HOME  /usr/lib/jvm/java-12-openjdk-12.0.1.12-1.rolling.el7.x86_64

ENV ES_VERSION 6.7.2

RUN groupadd -g 9200 elasticsearch && \
    adduser -u 9200 -g 9200 -d /usr/share/elasticsearch elasticsearch

WORKDIR /usr/share/elasticsearch

RUN cd /opt && curl --retry 8 -s -L -O https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-${ES_VERSION}.tar.gz && \
    cd /usr/share/elasticsearch

RUN tar zxf /opt/elasticsearch-${ES_VERSION}.tar.gz --strip-components=1 && \
    rm -rf /opt/elasticsearch-${ES_VERSION}.tar.gz 

RUN grep ES_DISTRIBUTION_TYPE=tar /usr/share/elasticsearch/bin/elasticsearch-env \
    && sed -ie 's/ES_DISTRIBUTION_TYPE=tar/ES_DISTRIBUTION_TYPE=docker/' /usr/share/elasticsearch/bin/elasticsearch-env
    
RUN mkdir -p config data logs

RUN chmod 0775 config data logs

COPY elasticsearch.yml log4j2.properties config/
COPY  docker-entrypoint.sh /usr/local/bin/docker-entrypoint.sh
 

RUN   chmod -R 0775 /usr/share/elasticsearch && \
    chgrp 0 /usr/share/elasticsearch

 
RUN chown 9200:0 /usr/local/bin/docker-entrypoint.sh &&  \
    chgrp 0 /usr/local/bin/docker-entrypoint.sh && \
    chmod g=u /etc/passwd && \
    chmod 0775 /usr/local/bin/docker-entrypoint.sh


##增加自定义路径/data/elasticsearch/
RUN  mkdir -p /data/elasticsearch/{data,logs}  && \
     chmod -R 0775 /data/elasticsearch/ &&\
     chown -R 9200:0 /data/elasticsearch/
 

EXPOSE 9200 9300
 

ENTRYPOINT ["/usr/local/bin/docker-entrypoint.sh"]


CMD ["eswrapper"]

###docker build -f Dockerfile -t="elasticsearch:6.7.2" .

