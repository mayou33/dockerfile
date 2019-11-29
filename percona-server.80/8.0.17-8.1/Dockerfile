FROM centos:8
##https://hub.docker.com/r/percona/percona-server/tags

##set timezone
ENV TZ=Asia/Shanghai
RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone


RUN groupadd -g 3306 mysql && useradd -u 3306 -r -g 3306 -s /sbin/nologin  mysql

RUN  rpm --import  https://repo.percona.com/yum/PERCONA-PACKAGING-KEY \
	&& yum -y install https://repo.percona.com/yum/percona-release-latest.noarch.rpm \ 
	&& dnf module disable mysql -y \
	&& percona-release disable all \
	&& yes| percona-release setup ps80

# install exact version of PS for repeatability
ENV PERCONA_VERSION 8.0.17-8.1.el8

RUN yum install -y \
		percona-server-server-${PERCONA_VERSION} \
        percona-server-devel-${PERCONA_VERSION} \ 
		percona-server-tokudb-${PERCONA_VERSION} \
		percona-server-rocksdb-${PERCONA_VERSION} \
		jemalloc \
		which \
		policycoreutils \
        openssl \
        openssl-devel \
        numactl \
	&& yum clean all \
	&& rm -rf /var/cache/yum /var/lib/mysql

# purge and re-create /var/lib/mysql with appropriate ownership
RUN /usr/bin/install -m 0775 -o mysql -g root -d /var/lib/mysql /var/run/mysqld /docker-entrypoint-initdb.d \
# comment out a few problematic configuration values
	&& find /etc/my.cnf /etc/my.cnf.d -name '*.cnf' -print0 \
		| xargs -0 grep -lZE '^(bind-address|log|user)' \
		| xargs -rt -0 sed -Ei 's/^(bind-address|log|user)/#&/' \
# don't reverse lookup hostnames, they are usually another container
	&& echo '!includedir /etc/my.cnf.d' >> /etc/my.cnf \
	&& printf '[mysqld]\nskip-host-cache\nskip-name-resolve\n' > /etc/my.cnf.d/docker.cnf \
# TokuDB modifications
	&& /usr/bin/install -m 0664 -o mysql -g root /dev/null /etc/sysconfig/mysql \
	&& echo "LD_PRELOAD=/usr/lib64/libjemalloc.so.1" >> /etc/sysconfig/mysql \
	&& echo "THP_SETTING=never" >> /etc/sysconfig/mysql \
# allow to change config files
	&& chown -R mysql:root /etc/my.cnf /etc/my.cnf.d \
	&& chmod -R ug+rwX /etc/my.cnf /etc/my.cnf.d

###RUN [ "/bin/bash", "-c", "mkdir -p /data/mysql/{data,conf,log,backup,undo}" ]
RUN  mkdir -p /data/mysql/data /data/mysql/conf /data/mysql/log /data/mysql/backup /data/mysql/undo && chown -R mysql:root /data/mysql/ && chmod -R ug+rwX /data/mysql/

VOLUME ["/var/lib/mysql", "/var/log/mysql", "/data/mysql/data/", "/data/mysql/log/", "/data/mysql/backup/", "/data/mysql/undo/", "/etc/my.cnf.d"]

COPY ps-entry.sh /docker-entrypoint.sh
RUN chmod 0755 /docker-entrypoint.sh && chown -R mysql:root /docker-entrypoint.sh
ENTRYPOINT ["/docker-entrypoint.sh"]

USER mysql
EXPOSE 3306
CMD ["mysqld"]

### docker build -f Dockerfile -t="percona-server:8.0.17" .