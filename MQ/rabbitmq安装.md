# rabbitmq安装
Docker安装
1，安装带有管理插件的版本，3.8.3是目前最新版
```
docker pull rabbitmq:3.8.3-management
```
2，创建docker容器启动
```

docker run -d --hostname my-rabbit --name some-rabbit -p 8080:15672 -p 5672:5672 rabbitmq:3.8.3-management
```
```
 --hostname：指定容器主机名称
 --name:指定容器名称
 -p:将mq端口号映射到本地
 15672：控制台端口号
 5672： 应用访问端口号
```

然后，您可以转到http://localhost:8080或http://host-ip:8080在浏览器中。
```
默认用户：  guest 
默认密码：  guest
```
p.s启动时设置账号密码
```
docker run -d --hostname my-rabbit --name rabbit -e RABBITMQ_DEFAULT_USER=admin -e RABBITMQ_DEFAULT_PASS=admin -p 15672:15672 -p 5672:5672 -p 25672:25672 -p 61613:61613 -p 1883:1883 rabbitmq:management
```