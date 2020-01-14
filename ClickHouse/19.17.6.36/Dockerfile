#docker build -f Dockerfile -t="clickhouse:19.17.6.36" .

FROM ubuntu:19.10
MAINTAINER zhangyu8.me


ARG repository="deb http://repo.yandex.ru/clickhouse/deb/stable/ main/"
ARG version=19.17.6.36
ARG gosu_ver=1.10

##set timezone
ENV TZ=Asia/Shanghai
RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone
##path
RUN [ "/bin/bash", "-c", "mkdir -p /data/clickhouse/{data,log,conf,tmp,user_files,format_schemas}" ]


RUN apt-get update \
    && apt-get install --yes --no-install-recommends \
        apt-transport-https \
        dirmngr \
        gnupg \
    && groupadd -r -g 9000 clickhouse \
    && useradd -r -u 9000  -d /nonexistent -s /bin/false -g clickhouse  clickhouse  \
    && chown -R clickhouse:clickhouse /data/clickhouse  \
    && mkdir -p /etc/apt/sources.list.d \
    && apt-key adv --keyserver keyserver.ubuntu.com --recv E0C56BD4 \
    && echo $repository > /etc/apt/sources.list.d/clickhouse.list \
    && apt-get update \
    && env DEBIAN_FRONTEND=noninteractive \
        apt-get install --allow-unauthenticated --yes --no-install-recommends \
            clickhouse-common-static=$version \
            clickhouse-client=$version \
            clickhouse-server=$version \
            locales \
            tzdata \
            wget \
    && rm -rf \
        /var/lib/apt/lists/* \
        /var/cache/debconf \
        /tmp/* \
    && apt-get clean

ADD https://github.com/tianon/gosu/releases/download/$gosu_ver/gosu-amd64 /bin/gosu

RUN locale-gen en_US.UTF-8
ENV LANG en_US.UTF-8
ENV LANGUAGE en_US:en
ENV LC_ALL en_US.UTF-8

RUN mkdir /docker-entrypoint-initdb.d

COPY docker_related_config.xml /etc/clickhouse-server/config.d/
##COPY config.xml /etc/clickhouse-server/config.xml
COPY entrypoint.sh /entrypoint.sh

RUN chmod +x \
    /entrypoint.sh \
    /bin/gosu

EXPOSE 9000 8123 9009

VOLUME ["/var/lib/clickhouse", "/var/log/clickhouse-server", "/data/clickhouse/data/", "/data/clickhouse/log/", "/data/clickhouse/tmp/", "/data/clickhouse/user_files/", "/data/clickhouse/format_schemas/", "/data/clickhouse/conf/"]

ENV CLICKHOUSE_CONFIG /etc/clickhouse-server/config.xml

ENTRYPOINT ["/entrypoint.sh"]