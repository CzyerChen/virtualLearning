### 基础命令
- docker search
- docker pull
- docker run
- docker images
- docker ps
    - docker ps -a /docker ps --all /docker ps -f过滤/docker ps --filter name=xxx
- docker version
- docker info
- docker rmi 镜像id
- docker  rm  容器id
- docker inspect 容器id  --- 查看容器详细内容
    - docker inspect -f {{.State.Pid}} 容器id -- 可以拿到程序的进程号
```text
bogon:$ docker inspect -f {{.State.Pid}} 5b8c5d7ae676
13433
```
- docker create
- docker start
- docker kill
- docker stop
- docker restart
- docker pause
- docker unpause
- docker stats -- 查看容器使用的资源情况，和Linux的top命令比较相似
    - docker top 容器id
    

### 远程容器仓库 hub
- docker login 
- docker logout
- docker push
- docker pull
- docker commit 
- dcoker save
- docker load


### 进入docker容器
首先运行一个软件 docker run -itd mysql:5.7 /bin/bash 
- 使用docker attach ---- docker attach 容器id 
    - 附加至某个运行容器的终端设备
    - 当多个窗口同时使用该命令进入该容器时，所有的窗口都会同步显示。如果有一个窗口阻塞了，那么其他窗口也无法再进行操作
- 使用ssh --- 使用docker 后不加你使用ssh 
- 使用nsenter --- 需要安装
```text
首先拿到容器中程序的进程号

nsenter --target 进程号 --mount --uts --ipc --net --pid  

```
- 使用exec  --- 最为常用，让运行中的容器运行一个额外的容器
```text
docker exec -it 容器id  /bin/bash

```

### 构建镜像
- docker build -t repo (这个目录下需要包含一个书写好的dockerfile,用于编译)
- 如果利用docker-compose也可以，书写一个docker-compose.yml文件，编写好容器，使用docker-compose build elk ,然后使用docker-compose up -d即可


### 辅助命令
- 插卡镜像ip
```text
docker inspect 容器ID | grep IPAddress
```
- 查看镜像名称
```text
sudo docker inspect -f='{{.Name}}' $(sudo docker ps -a -q)
```
- 查看镜像虚拟IP
```text
sudo docker inspect -f='{{.NetworkSettings.IPAddress}}' $(sudo docker ps -a -q)
```
- 列出镜像以及相关的端口
```text
 docker inspect -f='{{.Name}} {{.NetworkSettings.IPAddress}} {{.HostConfig.PortBindings}}' $(docker ps -aq)
```


### [所有命令集合](https://docs.docker.com/engine/reference/commandline/dockerd/)