# linux安装docker

1，卸载旧版本docker
全新安装时，无需执行该步骤
```
$ sudo apt-get remove docker docker-engine docker.io
```
2，更新系统软件
```
$ sudo apt-get update
```

3，安装依赖包
```
$ sudo apt-get install \ apt-transport-https \ ca-certificates \ curl \ software-properties-common
```

4，添加官方密钥
执行该命令时，如遇到长时间没有响应说明网络连接不到docker网站，需要使用代-理进行。
```
$ curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
```
显示OK,表示添加成功.

5，添加仓库
```
sudo add-apt-repository "deb [arch=amd64]  https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" 
```

6，再次更新软件
经实践，这一步不能够省略，我们需要再次把软件更新到最新，否则下一步有可能会报错。
```
$ sudo apt-get update
```

7，安装docker
如果想指定安装某一版本，可使用 sudo apt-get install docker-ce=<VERSION> 命令，把<VERSION>替换为具体版本即可。
以下命令没有指定版本，默认就会安装最新版
```
$ sudo apt-get install docker-ce
```

8，查看docker版本
```
$ sudo docker version
```

9，免sudo使用docker命令
如果还没有 docker group 就添加一个：
```
$ sudo groupadd docker
```

将用户加入该 group 内。然后退出并重新登录就生效啦
```
$ sudo gpasswd -a ${USER} docker
```

重启 docker 服务
```
$ sudo service docker restart
```

切换当前会话到新 group 或者重启 X 会话
```
$ newgrp - docker
```

