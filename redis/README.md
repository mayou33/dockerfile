docker pull centos:7.5.1804

 生成镜像
 
docker build -f Dockerfile -t="redis:3.2.12" .



查看 #docker images


启动redis

#docker run -d --name=redis3212 -p6379:6379 redis:3.2.12

 
#进入容器执行redis客户端

docker exec -it redis3212 redis-cli -a '123456'