> [以下文字来自于Yujiaao](https://segmentfault.com/a/1190000016781704)

> docker 以进程为核心, 对系统资源进行隔离使用的管理工具. 

>隔离是通过 cgroups (control groups 进程控制组) 这个操作系统内核特性来实现的. 包括用户的参数限制、 帐户管理、 资源(CPU,内存,磁盘I/O,网络)使用的隔离等. 

> docker 在运行时可以为容器内进程指定用户和组. 没有指定时默认是 root .但因为隔离的原因, 并不会因此丧失安全性.
 
> 传统上, 特定的应用都以特定的用户来运行, 在容器内进程指定运行程序的所属用户或组并不需要在 host 中事先创建.

进程控制组cgroups主要可能做以下几件事:
- 资源限制 组可以设置为不超过配置的内存限制, 其中还包括文件系统缓存
- 优先级 某些组可能会获得更大的 CPU 利用率份额或磁盘 i/o 吞吐量
- 帐号会计 度量组的资源使用情况, 例如, 用于计费的目的
- 控制 冻结组进程, 设置进程的检查点和重新启动

与 cgroups(控制进程组) 相关联的概念是 namespaces (命令空间).
命名空间主要有六种名称隔离类型:
- PID 命名空间为进程标识符 (PIDs) 的分配、进程列表及其详细信息提供了隔离。
虽然新命名空间与其他同级对象隔离, 但其 "父 " 命名空间中的进程仍会看到子命名空间中的所有进程 (尽管具有不同的 PID 编号)。
- 网络命名空间隔离网络接口控制器 (物理或虚拟)、iptables 防火墙规则、路由表等。网络命名空间可以使用 "veth " 虚拟以太网设备彼此连接。
- UTS 命名空间允许更改主机名。
- mount(装载)命名空间允许创建不同的文件系统布局, 或使某些装入点为只读。
- IPC 命名空间将 System V 的进程间通信通过命名空间隔离开来。
- 用户命名空间将用户 id 通过命名空间隔离开来。


#### 普通用户 docker run 容器内 root
- 如 busybox, 可以在 docker 容器中以 root 身份运行软件. 但 docker 容器本身仍以普通用户执行.
考虑这样的情况
```text
echo test | docker run -i busybox cat
```
前面的是当前用户当前系统进程,后面的转入容器内用户和容器内进程运行.
当在容器内 PID 以1运行时, Linux 会忽略信号系统的默认行为, 进程收到 SIGINT 或 SIGTERM 信号时不会退出, 除非你的进程为此编码. 可以通过 Dockerfile STOPSIGNAL signal指定停止信号.
如:
```text
STOPSIGNAL SIGKILL
```
创建一个 Dockerfile
```text
FROM alpine:latest
RUN apk add --update htop && rm -rf /var/cache/apk/*
CMD ["htop"]
$ docker build -t myhtop .  #构建镜像
$ docker run -it --rm --pid=host myhtop #与 host 进程运行于同一个命名空间
```

#### 普通用户 docker run 容器内指定不同用户 demo_user
```text
docker run --user=demo_user:group1 --group-add group2 <image_name> <command>
```
- 这里的 demo_user 和 group1(主组), group2(副组) 不是主机的用户和组, 而是创建容器镜像时创建的.
- 当Dockerfile里没有通过USER指令指定运行用户时, 容器会以 root 用户运行进程.

#### docker 指定用户的方式
#### Dockerfile 中指定用户运行特定的命令
```text
USER <user>[:<group>] #或
USER <UID>[:<GID>]
```
```text
docker run -u(--user)[user:group] 或 --group-add 参数方式
$ docker run  busybox cat /etc/passwd
root:x:0:0:root:/root:/bin/sh
...
www-data:x:33:33:www-data:/var/www:/bin/false
nobody:x:65534:65534:nobody:/home:/bin/false

$ docker run  --user www-data busybox id
uid=33(www-data) gid=33(www-data)
```

#### docker 容器内用户的权限
- 对比以下情况, host 中普通用户创建的文件, 到 docker 容器下映射成了 root 用户属主:
```text
$ mkdir test && touch test/a.txt && cd test
$ docker run --rm -it -v `pwd`:/mnt -w /mnt  busybox   /bin/sh -c 'ls -al /mnt/*' 
-rw-r--r--    1 root     root             0 Oct 22 15:36 /mnt/a.txt
```
而在容器内卷目录中创建的文件, 则对应 host 当前执行 docker 的用户:
```text
$ docker run --rm -it -v `pwd`:/mnt -w /mnt  busybox   /bin/sh -c 'touch b.txt'
$ ls -al
-rw-r--r--  1 xwx  staff    0 10 22 23:36 a.txt
-rw-r--r--  1 xwx  staff    0 10 22 23:54 b.txt
```
#### docker volume 文件访问权限
- 创建和使用卷, docker 不支持相对路径的挂载点, 多个容器可以同时使用同一个卷.
```text
$ docker volume create hello #创建卷

hello

$ docker run -it --rm  -v hello:/world -w /world busybox /bin/sh -c 'touch /world/a.txt &&  ls -al'   #容器内建个文件
total 8
drwxr-xr-x    2 root     root          4096 Oct 22 16:38 .
drwxr-xr-x    1 root     root          4096 Oct 22 16:38 ..
-rw-r--r--    1 root     root             0 Oct 22 16:38 a.txt

$ docker run -it --rm  -v hello:/world -w /world busybox /bin/sh -c 'rm /world/a.txt &&  ls -al' #从容器内删除
total 8
drwxr-xr-x    2 root     root          4096 Oct 22 16:38 .
drwxr-xr-x    1 root     root          4096 Oct 22 16:38 ..
```
外部创建文件, 容器内指定用户去删除
```text
$ touch c.txt && sudo chmod root:wheel c.txt
$ docker run -u 100 -it --rm  -v `pwd`:/world -w /world busybox /bin/sh -c 'rm /world/c.txt &&  ls -al'
```
实际是可以删除的
```text
rm: remove '/world/c.txt'? y
total 4
drwxr-xr-x    4 100      root           128 Oct 23 16:09 .
drwxr-xr-x    1 root     root          4096 Oct 23 16:09 ..
-rw-r--r--    1 100      root             0 Oct 22 15:36 a.txt
-rw-r--r--    1 100      root             0 Oct 22 15:54 b.txt
```
#### docker 普通用户的1024以下端口权限
```text
$ docker run -u 100 -it --rm  -p 70:80 busybox /bin/sh -c 'nc -l -p 80'
nc: bind: Permission denied  #用户id 100 时, 不能打开80端口
$ docker run -u 100 -it --rm  -p 70:8800 busybox /bin/sh -c 'nc -l -p 8800'  #容器端口大于1024时则可以
...
$ docker run -it --rm  -p 70:80 busybox /bin/sh -c 'nc -l -p 80'  #容器内是 root 也可以
...
```
#### docker-compose如何使用用户和组呢？
- docker :docker run -u user iamge
- docker-compose :在编写编排文件的时候，可以指定user：可以使用username也可以使用uid，但是有一点区别的是，如果是用username,那么容器里面也必须存在这个同样名字的user，如果是使用uid，那么就没有这个要求
```text
version: '3'
services:
  rspec:
    image: app:latest
    environment:
    user: ${CURRENT_UID} #运用当前用户的UID
    volumes:
      - .:/app
```
- docker-compose使用当前用户：CURRENT_UID=$(id -u):$(id -g) docker-compose up
- docker-compose使用指定用户：CURRENT_UID=$(id -uuser):$(id -g user) docker-compose up
- 注意：让容器用指定用户的运行的前提是：容器中有这个用户，不然容器中的程序还是会使用root运行
```text
因而可以直接在dockerfile中就先添加user，再指定user

FROM alpine

RUN mkdir /app
COPY code /app/
RUN adduser -S  infra
RUN echo 'user:hello' | chpasswd 
USER user
CMD /app/code
```
- 最后可以进入容器，查看当前用户 whoami
