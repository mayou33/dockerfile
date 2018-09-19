FROM centos:7.5.1804

###########基本软件############
RUN \
 yum -y install yum-priorities epel-release  && \
 yum-config-manager --enable epel  && \
 yum -y install gcc gcc-c++ iotop nc iptraf wget vim unzip atop make cmake autoconf automake sysstat lsof python-pip htop iftop nload nethogs net-tools tar openssl openssl-devel python-devel iproute && \ 
 yum clean all && \ 
 rm -rf /var/cache/yum 
###########优化############
RUN \
 rm -rf /etc/localtime  && \
 ln -s /usr/share/zoneinfo/Asia/Shanghai /etc/localtime   && \
 echo "alias ll='ls -alhF --color=auto'" >> /etc/bashrc && \
 userdel  ftp && \
 userdel  nobody && \
 groupdel   games && \
 groupdel  video && \
 groupdel  audio && \
 sed -i "s/4096/100000/" /etc/security/limits.d/20-nproc.conf
  

###优化sysctl.conf
ADD sysctl.conf /usr/lib/sysctl.d/50-default.conf

############
RUN  \
cat /dev/null > /var/log/wtmp && \
cat /dev/null > /var/log/lastlog && \
cat /dev/null > /var/log/yum.log && \
history -c && history -w

CMD ["/usr/sbin/init"]