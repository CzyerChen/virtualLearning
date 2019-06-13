> docker中有内部虚拟的IP 有不同的网络模式，能够帮助使用者访问容器内部的应用
- 在使用docker run创建Docker容器时，可以用--net选项指定容器的网络模式

```text
host模式，使用--net=host指定。

container模式，使用--net=container:NAME_or_ID指定。

none模式，使用--net=none指定。

bridge模式，使用--net=bridge指定，默认设置。
```

### host模式
- 直接就使用宿主机的IP 和端口，因而就和本地运行一样的效果，但是文件资源、进程资源都是和宿主机是隔离，不相关的

### container 模式
- 这种模式下，内部容器就可以共享一个IP，不用分配多余的虚拟IP，定新创建的容器和已经存在的一个容器共享一个Network Namespace，而不是和宿主机共享
- 新创建的容器不会创建自己的网卡，配置自己的IP，而是和一个指定的容器共享IP、端口范围等
- 两个容器除了网络方面，其他的如文件系统、进程列表等还是隔离的
- 两个容器的进程可以通过lo网卡设备通信

### none 模式
- Docker容器拥有自己的Network Namespace，并不为Docker容器进行任何网络配置
- 这个Docker容器没有网卡、IP、路由等信息，需要我们自己为Docker容器添加网卡、配置IP等


### bridge模式
- Docker默认的网络设置
- 此模式会为每一个容器分配Network Namespace、设置IP等，并将一个主机上的Docker容器连接到一个虚拟网桥上

- 例子:内部访问外部 ，使用snat的转换
```text
假设主机有一块网卡为eth0，IP地址为10.10.101.105/24，网关为10.10.101.254。
从主机上一个IP为172.17.0.1/16的容器中ping百度（180.76.3.151）。
IP包首先从容器发往自己的默认网关docker0，包到达docker0后，也就到达了主机上。
然后会查询主机的路由表，发现包应该从主机的eth0发往主机的网关10.10.105.254/24。
接着包会转发给eth0，并从eth0发出去（主机的ip_forward转发应该已经打开）。
这时候，上面的Iptable规则就会起作用，对包做SNAT转换，将源地址换为eth0的地址。
这样，在外界看来，这个包就是从10.10.101.105上发出来的，Docker容器对外是不可见的。
```
- 例子：外部访问内部，利用snat做转换
```text
-A DOCKER ! -i docker0 -p tcp -m tcp --dport 80 -j DNAT --to-destination 172.17.0.5:80

iptable有以上这样的一条变化，因而转发到宿主机的80的请求，会转发到docker内部容器

```
- 可以自定义Docker使用的IP地址、DNS等信息，甚至使用自己定义的网桥