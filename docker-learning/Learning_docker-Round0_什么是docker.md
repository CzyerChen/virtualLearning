- docker 在2017年，进行一阶段改造，开源社区改名Moby后，就推出了docker-ce(免费版)和docker-ee（企业收费版），成为了docker公司的产品
- 我们平常所下载的docker就是目前的一款docker免费版产品，它取之于开源项目Moby，[docker 改名moby](https://www.zhihu.com/question/58805021)



### docker的可视化页面
```text
版本要求：
     必须要2010年以后的Mac电脑，支持MMU虚拟化，可以用以下命令来检查
     MacBook-Pro:~ user$ sysctl kern.hv_support
     kern.hv_support: 1
     操作系统macOS Sierra 10.12或者更新的系统
     4G以上的内存
     不能按照VirtualBox早于version 4.3.30的版本

```
- Mac 版本满足要求，就可以直接下载docker-ce产品，不论是通过brew下载或是网页下载
- docker-ce产品自身就包括了，docker engine/kitemetic/virtualbox/Docker CLI client/Docker Compose/Docker Machine这一堆，本来是要一个个手装的东西，真的要感慨一下，十分方便，但是一开始还是没搞清楚，走了弯路


### docker 镜像服务器
- 阿里云：需要注册阿里的账号，在后台控制台，找到加速器，每人会给一个单独的地址
- 其他：https://reg-mirror.qiniu.com
- 其他：https://dockerhub.azk8s.cn


### 容器状态
- created
- running
- paused
- stopped
- deleted

### docker info 查看容器情况
```text
bogon:elasticsearch zzzz$ docker info
Containers: 9
 Running: 0
 Paused: 0
 Stopped: 9
Images: 6
Server Version: 18.09.2
Storage Driver: overlay2
 Backing Filesystem: extfs
 Supports d_type: true
 Native Overlay Diff: true
Logging Driver: json-file
Cgroup Driver: cgroupfs
Plugins:
 Volume: local
 Network: bridge host macvlan null overlay
 Log: awslogs fluentd gcplogs gelf journald json-file local logentries splunk syslog
Swarm: inactive
Runtimes: runc
Default Runtime: runc
Init Binary: docker-init
containerd version: 9754871865f7fe2f4e74d43e2fc7ccd237edcbce
runc version: 09c8266bf2fcf9519a651b04ae54c967b9ab86ec
init version: fec3683
Security Options:
 seccomp
  Profile: default
Kernel Version: 4.9.125-linuxkit
Operating System: Docker for Mac
OSType: linux
Architecture: x86_64
CPUs: 4
Total Memory: 1.952GiB
Name: linuxkit-025000000001
ID: UUQ6:IFGY:OER5:PUQG:3DLM:B4F3:LUKH:BCIT:XUKN:BWAT:XXIY:K6PX
Docker Root Dir: /var/lib/docker
Debug Mode (client): false
Debug Mode (server): true
 File Descriptors: 27
 Goroutines: 54
 System Time: 2019-06-12T04:45:39.111281Z
 EventsListeners: 2
HTTP Proxy: gateway.docker.internal:3128
HTTPS Proxy: gateway.docker.internal:3129
Registry: https://index.docker.io/v1/
Labels:
Experimental: false
Insecure Registries:
 127.0.0.0/8
Registry Mirrors:
 https://1phgdzht.mirror.aliyuncs.com/
 https://dockerhub.azk8s.cn/
 https://reg-mirror.qiniu.com/
Live Restore Enabled: false

```
### 应用场景
- 简化配置，主要是减轻了vm的存在，将应用和环境的配置最小化
- 代码流水线化管理，从开发到应用部署，都可以实现开发和线上一致的环境
- 提高开发效率
- 隔离应用：docker一个很显著的特点就是资源的隔离，包括服务器的资源，进程资源、文件资源、网络资源等等
- 整合服务器：由于节约了资源，也实现了通过虚拟机可以更好的整合应用
- 调试能力
- 多租户环境，给不同租户在应用层创建独立的环境，做到真正的隔离，底层基础服务就可以复用
- 快速部署，方便运维，对于服务的部署和运维的成本可以大大降低，也可以大大降低门槛，不用用户和组，不用复杂的网络配置


### 其他
- 网络配置
- 容器资源限制
- nginx反向代理
- 容器编排策略
```text
kubernates

mesos+marathon

machine+swarn+compose

```

```text
REPOSITORY                                      TAG                 IMAGE ID            CREATED             SIZE
appbaseio/dejavu                                latest              864c47a02bbc        47 hours ago        134MB    ------ 不好用
nshou/elasticsearch-kibana                      latest              5e2b8e1ec0e2        2 weeks ago         383MB    ------ 做了一个es 和kibana的简单整合，可以测试使用
kibana                                          6.7.1               860831fbf9e7        2 months ago        677MB
elasticsearch                                   6.7.1               e2667f5db289        2 months ago        812MB
sebp/elk                                        670                 475d33974662        2 months ago        1.58GB
```