FROM centos:7
MAINTAINER zhangyu
##https://github.com/percona/percona-docker/tree/master/percona-server-mongodb.40

COPY C.utf8 /usr/lib/locale/C.utf8
ENV LANG C.UTF-8
ENV LC_ALL C.UTF-8
##set timezone
ENV TZ=Asia/Shanghai
RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone
##安装中文
RUN yum -y install kde-l10n-Chinese && yum -y reinstall glibc-common  


# the numeric UID is needed for OpenShift
RUN mkdir -p /home/mongodb  && touch /home/mongodb/.dbshell &&  touch  /home/mongodb/.mongorc.js && useradd -u 27017 -r -g 0 -d /home/mongodb -s /sbin/nologin mongodb && chown -R 27017.0 /home/mongodb


ENV PERCONA_VERSION 4.0.10-5.el7
ENV K8S_TOOLS_VERSION 0.4.2

####手动下

RUN  yum install -y epel-release jq wget shadow-utils curl libpcap policycoreutils libselinux-utils \ 
   && wget -P /tmp/ http://repo.percona.com/psmdb-40/yum/release/7/RPMS/x86_64/percona-server-mongodb-mongos-${PERCONA_VERSION}.x86_64.rpm \ 
   && wget -P /tmp/ http://repo.percona.com/psmdb-40/yum/release/7/RPMS/x86_64/percona-server-mongodb-server-${PERCONA_VERSION}.x86_64.rpm \ 
   && wget -P /tmp/  http://repo.percona.com/psmdb-40/yum/release/7/RPMS/x86_64/percona-server-mongodb-shell-${PERCONA_VERSION}.x86_64.rpm \ 
   && wget -P /tmp/ http://repo.percona.com/psmdb-40/yum/release/7/RPMS/x86_64/percona-server-mongodb-tools-${PERCONA_VERSION}.x86_64.rpm  \ 
   && rpm -ivh /tmp/percona-server-mongodb*.rpm

RUN yum clean all  &&  rm -rf /var/cache/yum /data/db && rm -rf /tmp/* && mkdir -p /data/db && mkdir -p /data/mongodbbackup \
        && chown -R 27017:0 /data/db && chown -R 27017:0 /data/mongodbbackup

 
RUN curl -fSL https://github.com/percona/mongodb-orchestration-tools/releases/download/${K8S_TOOLS_VERSION}/k8s-mongodb-initiator -o /usr/local/bin/k8s-mongodb-initiator \
    && curl -fSL  https://github.com/percona/mongodb-orchestration-tools/releases/download/${K8S_TOOLS_VERSION}/mongodb-healthcheck -o /usr/local/bin/mongodb-healthcheck \
    && chmod 0755 /usr/local/bin/k8s-mongodb-initiator /usr/local/bin/mongodb-healthcheck

VOLUME ["/data/db","/data/mongodbbackup"]


COPY ps-entry.sh /entrypoint.sh

RUN chown 27017:0 /entrypoint.sh && chmod 0775 /entrypoint.sh


ENTRYPOINT ["/entrypoint.sh"]


EXPOSE 27017

USER 27017

CMD ["mongod"]
####docker build -f Dockerfile -t="percona-server-mongodb:4.0.10" . 