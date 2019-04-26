FROM centos:7
MAINTAINER zhangyu

COPY C.utf8 /usr/lib/locale/C.utf8
ENV LANG C.UTF-8
ENV LC_ALL C.UTF-8
##set timezone
ENV TZ=Asia/Shanghai
RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone
##安装中文
RUN yum -y install kde-l10n-Chinese && yum -y reinstall glibc-common  


# the numeric UID is needed for OpenShift
RUN useradd -u 27017 -r -g 0  mongodb

ENV PERCONA_MAJOR 40
ENV PERCONA_VERSION 4.0.9-4.el7
ENV K8S_TOOLS_VERSION 0.4.1

####或者手动下

RUN  yum install -y epel-release jq wget curl libpcap policycoreutils libselinux-utils \ 
   && wget -P /tmp/ http://repo.percona.com/psmdb-40/yum/release/7/RPMS/x86_64/percona-server-mongodb-mongos-${PERCONA_VERSION}.x86_64.rpm \ 
   && wget -P /tmp/ http://repo.percona.com/psmdb-40/yum/release/7/RPMS/x86_64/percona-server-mongodb-server-${PERCONA_VERSION}.x86_64.rpm \ 
   && wget -P /tmp/  http://repo.percona.com/psmdb-40/yum/release/7/RPMS/x86_64/percona-server-mongodb-shell-${PERCONA_VERSION}.x86_64.rpm \ 
   && wget -P /tmp/ http://repo.percona.com/psmdb-40/yum/release/7/RPMS/x86_64/percona-server-mongodb-tools-${PERCONA_VERSION}.x86_64.rpm  \ 
   && rpm -ivh /tmp/percona-server-mongodb*.rpm
    


###yum 安装
# check repository package signature in secure way
##RUN export GNUPGHOME="$(mktemp -d)" \
##        && gpg --keyserver 'hkp://p80.pool.sks-keyservers.net:80' --recv-keys 430BDF5C56E7C94E848EE60C1C4CBDCDCD2EFD2A \
##        && gpg --export --armor 430BDF5C56E7C94E848EE60C1C4CBDCDCD2EFD2A > ${GNUPGHOME}/RPM-GPG-KEY-Percona \
##        && rpmkeys --import ${GNUPGHOME}/RPM-GPG-KEY-Percona /etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7 \
##        && curl -L -o /tmp/percona-release.rpm https://repo.percona.com/percona/yum/percona-release-1.0-7.noarch.rpm \
##        && rpmkeys --checksig /tmp/percona-release.rpm \
##        && yum install -y /tmp/percona-release.rpm \
##        && rm -rf "$GNUPGHOME" /tmp/percona-release.rpm \
##        && rpm --import /etc/pki/rpm-gpg/PERCONA-PACKAGING-KEY \
##        && percona-release disable all \
##        && percona-release enable psmdb-40 release


##RUN  yum install -y epel-release \   
##        && rpm --import /etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7 \ 
##        && rpm --import /etc/pki/rpm-gpg/RPM-GPG-KEY-EPEL-7  \       
##        && yum install -y \
##                percona-server-mongodb-server-${PERCONA_VERSION} \
##                percona-server-mongodb-mongos-${PERCONA_VERSION} \
##                percona-server-mongodb-shell-${PERCONA_VERSION} \
##                percona-server-mongodb-tools-${PERCONA_VERSION} \
##                curl \
##                jq \
##        && yum clean all  
        
        

RUN yum clean all  &&  rm -rf /var/cache/yum /data/db  && mkdir -p /data/db && mkdir -p /data/mongodbbackup \
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
####docker build -f Dockerfile -t="mongodb:4.0.9" . 