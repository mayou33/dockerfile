FROM centos:7.5.1804
MAINTAINER zhangyu33.com



##注意官方centos7 镜像没有C.UTF-8，只有C 和en_US.utf8  要自己定制
###########基本软件############
RUN \
yum install -y wget \    
        && rm -rf /etc/yum.repos.d/{CentOS-Base.repo,epel.repo} \
        && wget -O /etc/yum.repos.d/CentOS7-Base-163.repo http://mirrors.163.com/.help/CentOS7-Base-163.repo \
        && wget -O /etc/yum.repos.d/epel-7-aliyun.repo http://mirrors.aliyun.com/repo/epel-7.repo \
        && yum makecache \
        && yum -y install gcc gcc-c++ iotop nc iptraf wget vim unzip atop make cmake autoconf automake sysstat lsof python-pip htop iftop nload nethogs net-tools tar openssl openssl-devel python-devel iproute  \ 
        && yum clean all  \ 
        && rm -rf /var/cache/yum 
 
##yum -y install yum-priorities epel-release  && \
###yum-config-manager --enable epel  && \
 
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
