FROM centos:7.5.1804
MAINTAINER zhangyu33.com
###########基本软件############
RUN \
 yum -y install yum-priorities epel-release  && \
 yum-config-manager --enable epel  && \
 yum -y install gcc gcc-c++ iotop nc iptraf wget vim unzip atop make cmake autoconf automake sysstat lsof python-pip htop iftop nload nethogs net-tools tar openssl openssl-devel python-devel iproute && \ 
 yum clean all && \ 
 rm -rf /var/cache/yum 
 
###########优化############

ADD sysctl.conf /usr/lib/sysctl.d/50-default.conf
ADD profile  /etc/profile

RUN \
 rm -rf /etc/localtime  && \
 ln -s /usr/share/zoneinfo/Asia/Shanghai /etc/localtime   && \
 echo "alias ll='ls -alhF --color=auto'" >> /etc/bashrc && \
 userdel  ftp && \
 groupdel   games && \
 groupdel  video && \
 groupdel  audio && \
 sed -i "s/4096/100000/" /etc/security/limits.d/20-nproc.conf && \
 chmod 777 /etc/rc.d/rc.local  &&\
 echo 'source /etc/profile' >> /etc/rc.d/rc.local && \
 echo 'source /etc/bashrc' >> /etc/rc.d/rc.local  && \
 echo '/bin/bash' >> /etc/rc.d/rc.local
 
############
RUN  \
cat /dev/null > /var/log/wtmp && \
cat /dev/null > /var/log/lastlog && \
cat /dev/null > /var/log/yum.log && \
history -c && history -w

ENTRYPOINT /bin/bash /etc/rc.d/rc.local

CMD ["/usr/sbin/init"]
