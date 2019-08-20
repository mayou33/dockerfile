FROM centos:7
MAINTAINER zhangyu
##https://hub.docker.com/r/percona/percona-server/tags
##注意官方centos7 镜像没有C.UTF-8，只有C 和en_US.utf8  要自己定制
COPY C.utf8 /usr/lib/locale/C.utf8
ENV LANG C.UTF-8
ENV LC_ALL C.UTF-8
##set timezone
ENV TZ=Asia/Shanghai
RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone
##安装中文
RUN yum -y install kde-l10n-Chinese && yum -y reinstall glibc-common 

RUN groupadd -g 3306 mysql
RUN useradd -u 3306 -r -g 3306 -s /sbin/nologin  mysql

# check repository package signature in secure way
RUN export GNUPGHOME="$(mktemp -d)" \
	&& gpg --keyserver ha.pool.sks-keyservers.net --recv-keys 430BDF5C56E7C94E848EE60C1C4CBDCDCD2EFD2A \
	&& gpg --export --armor 430BDF5C56E7C94E848EE60C1C4CBDCDCD2EFD2A > ${GNUPGHOME}/RPM-GPG-KEY-Percona \
	&& rpmkeys --import ${GNUPGHOME}/RPM-GPG-KEY-Percona /etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7 \
	&& curl -L -o /tmp/percona-release.rpm https://repo.percona.com/yum/percona-release-latest.noarch.rpm \
	&& rpmkeys --checksig /tmp/percona-release.rpm \
	&& yum install -y /tmp/percona-release.rpm \
	&& rm -rf "$GNUPGHOME" /tmp/percona-release.rpm \
	&& rpm --import /etc/pki/rpm-gpg/PERCONA-PACKAGING-KEY \
	&& percona-release disable all \
	&& percona-release setup ps80

# install exact version of PS for repeatability
ENV PERCONA_VERSION 8.0.16-7.1.el7

RUN yum install -y \
		percona-server-server-${PERCONA_VERSION} \
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


RUN mkdir -p /data/mysql/{data,conf,log}  &&  mkdir -p /data/mysql/mysql/share && chown -R mysql:root /data/mysql/ && chmod -R ug+rwX /data/mysql/

VOLUME ["/var/lib/mysql", "/var/log/mysql", "/data/mysql/data/", "/data/mysql/log/", "/etc/my.cnf.d"]

COPY ps-entry.sh /docker-entrypoint.sh
RUN chmod 0755 /docker-entrypoint.sh && chown -R mysql:root /docker-entrypoint.sh
ENTRYPOINT ["/docker-entrypoint.sh"]

USER mysql
EXPOSE 3306 33060
CMD ["mysqld"]

### docker build -f Dockerfile -t="percona-server:8.0.16" .