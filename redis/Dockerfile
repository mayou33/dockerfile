FROM centos:7.5.1804
RUN \
 yum -y install yum-priorities epel-release  && \
 yum-config-manager --enable epel  && \
 yum install redis -y && \
 yum clean all

RUN \
 rm -rf /etc/localtime  && \
 ln -s /usr/share/zoneinfo/Asia/Shanghai /etc/localtime   && \
 echo "alias ll='ls -alhF --color=auto'" >> /etc/bashrc && \
 sed -i "s/4096/100000/" /etc/security/limits.d/20-nproc.conf

RUN \
 echo "vm.overcommit_memory=1" >> /etc/sysctl.conf  && \
 echo "vm.swapniess=1" >> /etc/sysctl.conf && \
 echo "net.ipv4.tcp_max_syn_backlog = 3240000" >> /etc/sysctl.conf && \
 echo "net.core.somaxconn = 1000000" >> /etc/sysctl.conf && \
 echo "echo never > /sys/kernel/mm/transparent_hugepage/enabled" >>/etc/rc.d/rc.local && \
 echo "echo never > /sys/kernel/mm/transparent_hugepage/enabled" >>/etc/rc.d/rc.local

#修改绑定IP地址
RUN sed -i -e 's@bind 127.0.0.1@bind 0.0.0.0@g' /etc/redis.conf
#关闭保护模式
RUN sed -i -e 's@protected-mode yes@protected-mode no@g' /etc/redis.conf
#设置密码
RUN echo "requirepass 123456" >> /etc/redis.conf

EXPOSE 6379
#启动
ENTRYPOINT [ "/usr/bin/redis-server","/etc/redis.conf"]
CMD []
