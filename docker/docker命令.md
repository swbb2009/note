# docker命令
Docker使用说明
1，	列出本地主机的镜像: 
```
docker images
```
2，	在网上查找镜像: 
```
docker search 镜像名
```
3，	下载镜像：
```
docker pull 镜像名
```
4，	删除镜像:  
```
docker rmi 镜像名
```

5，	
```
创建tomcat容器并运行，将容器端口号8080映射到宿主机端口8088
 docker run -d -p 8088:8080 【可选--name 别名】tomcat
停止tomcat容器
docker stop 【容器id或者别名】
重新启动tomcat容器
docker start -i 【容器名或者别名】
删除创建好的tomcat容器
docker rm 【容器id】
删除全部容器
docker rm $(docker ps -aq)
停用全部运行中的容器
docker stop $(docker ps -q)
停用并删除全部容器
docker stop $（docker pa -q） & docker rm $(docker ps -aq)
```
6，	进入tomcat容器内部 
```
docker exec -it 【容器id】 /bin/bash
```
退出tomcat容器
```
exit
```
7，	复制war包到tomcat容器webapps下(这种情况下在tomcat容器关闭后，war包及其他文件都会消失)
```
docker cp 【war包原路径】【容器id】:/usr/local/tomcat/webapps
```
8，	将宿主机的文件挂载到容器中，相当于共享文件夹（这样只要把war包放在宿主机上的文件夹中，容器的tomcat中也会有这个文件）
```
docker run -d -p 8088:8080 --name tomcat -v 【宿主文件夹路径】:【tomcat路径】 tomcat
--name tomcat 取别名，之后可以通过‘tomcat’代替‘容器id‘
```
	
```
删除所有已经停止的容器 docker rm $(docker ps -a -q)
　　杀死所有正在运行的容器
docker kill $(docker ps -a -q)


        删除所有已经停止的容器
docker rm $(docker ps -a -q)


        删除所有未打 dangling 标签的镜像
docker rmi $(docker images -q -f dangling=true)


         删除所有镜像
docker rmi $(docker images -q)


        强制删除镜像名称中包含“doss-api”的镜像
docker rmi --force $(docker images | grep doss-api | awk '{print $3}')

 

 

　　删除所有未使用数据

docker system prune

　　只删除未使用的volumes

docker volume prune
```