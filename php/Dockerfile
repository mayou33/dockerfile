FROM centos7:7.5
MAINTAINER zhangyu33.com

###镜像centos7:7.5 用的是我的其他dockerfile生成的
####centos7 安装 基础环境
RUN \
 yum -y install libxml2 libxml2-devel openssl openssl-devel curl-devel libjpeg-devel libpng-devel freetype-devel gcc gcc-c++ autoconf libjpeg libjpeg-devel libpng libpng-devel freetype freetype-devel  zlib zlib-devel glibc glibc-devel glib2 glib2-devel bzip2 bzip2-devel ncurses ncurses-devel curl curl-devel ssse2fsprogs e2fsprogs-devel krb5 krb5-devel libidn libidn-devel openssl openssl-devel openldap openldap-devel nss_ldap openldap-clients openldap-servers gettext-devel libXpm-devel libtool libevent libevent-devel gd-devel gd libmcrypt libmcrypt-devel pcre pcre-devel php-mcrypt libmcrypt libmcrypt-devel re2c MariaDB-devel php-snmp net-snmp tidy libtidy-devel libxslt libxslt-devel


###########优化############
##php5.5.30-已经编译好了-从宿主机拷贝进去直接用
 
ADD php5.5.30.tar /usr/local/

 RUN \
 useradd -M -s /sbin/nologin nginx && \
 chmod -R 755 /usr/local/php && \
 echo 'export PATH=$PATH:/usr/local/php/bin' >> /etc/profile && \
 mkdir -p /data/logs/php/  && \
 sed -i '/touch/a\/usr/local/php/sbin/php-fpm'  /etc/rc.d/rc.local


EXPOSE 9000
 
ENTRYPOINT /bin/bash /etc/rc.d/rc.local

CMD ["/usr/sbin/init"]

#####docker build -f Dockerfile -t="php5:5.5.30" . 