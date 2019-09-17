FROM centos:7
MAINTAINER zhangyu
##https://github.com/percona/percona-docker/tree/master/percona-server-mongodb.42

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


ENV PERCONA_VERSION 4.2.0-1.el7
ENV K8S_TOOLS_VERSION 0.4.2

####手动下

RUN curl -L -o /tmp/jq.rpm https://download.fedoraproject.org/pub/epel/7/x86_64/Packages/j/jq-1.5-1.el7.x86_64.rpm \
	&& curl -L -o /tmp/oniguruma.rpm https://download.fedoraproject.org/pub/epel/7/x86_64/Packages/o/oniguruma-5.9.5-3.el7.x86_64.rpm \
	&& rpmkeys --checksig /tmp/jq.rpm /tmp/oniguruma.rpm \
	&& rpm -i /tmp/jq.rpm /tmp/oniguruma.rpm \
	&& rm -rf /tmp/jq.rpm /tmp/oniguruma.rpm

RUN  yum install -y epel-release wget shadow-utils curl libpcap policycoreutils libselinux-utils numactl \ 
   && wget -P /tmp/ http://repo.percona.com/psmdb-42/yum/release/7/RPMS/x86_64/percona-server-mongodb-${PERCONA_VERSION}.x86_64.rpm \
   && wget -P /tmp/ http://repo.percona.com/psmdb-42/yum/release/7/RPMS/x86_64/percona-server-mongodb-mongos-${PERCONA_VERSION}.x86_64.rpm \ 
   && wget -P /tmp/ http://repo.percona.com/psmdb-42/yum/release/7/RPMS/x86_64/percona-server-mongodb-server-${PERCONA_VERSION}.x86_64.rpm \ 
   && wget -P /tmp/  http://repo.percona.com/psmdb-42/yum/release/7/RPMS/x86_64/percona-server-mongodb-shell-${PERCONA_VERSION}.x86_64.rpm \ 
   && wget -P /tmp/ http://repo.percona.com/psmdb-42/yum/release/7/RPMS/x86_64/percona-server-mongodb-tools-${PERCONA_VERSION}.x86_64.rpm  \ 
   && rpm -ivh /tmp/percona-server-mongodb*.rpm
 

RUN yum clean all  &&  rm -rf /var/cache/yum /data/db && rm -rf /tmp/* && mkdir -p /data/db && mkdir -p /data/mongodbbackup \
        && chown -R 27017:0 /data/db && chown -R 27017:0 /data/mongodbbackup


COPY LICENSE /licenses/LICENSE.Dockerfile
RUN cp /usr/share/doc/percona-server-mongodb-server-$(echo ${PERCONA_VERSION} | cut -d - -f 1)/GNU-AGPL-3.0 /licenses/LICENSE.Percona-Server-for-MongoDB

ENV GOSU_VERSION=1.11
RUN curl -L -o /usr/bin/gosu https://github.com/tianon/gosu/releases/download/${GOSU_VERSION}/gosu-amd64 \
	&& chmod +x /usr/bin/gosu \
    && curl -o /licenses/LICENSE.gosu \
		https://raw.githubusercontent.com/tianon/gosu/${GOSU_VERSION}/LICENSE
 
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
####docker build -f Dockerfile -t="percona-server-mongodb:4.2.0" . 