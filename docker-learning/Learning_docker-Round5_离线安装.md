> 离线安装可以说是非常痛苦，非常惨了

> 首先考虑版本所需的所有压缩包，确认源码安装或者rpm包安装，下载完毕等待备用

> 离线安装还有很多想不到的就是依赖包，有docker 所需的依赖包，和系统所需更新版本的依赖包，因而系统版本和docker 版本是非常紧密的

> 还有很多网上互相抄袭，没有实践或者不说清楚版本的那些伪指南，可以说是相当可恨了

> 下面总结步骤：

CentOS Linux release 7.6.1810 (Core)

docker 18.09

> 真的要说明版本差异还是很大，centos 7.4对应docker 18.06，如果版本错开，会有很多依赖上的问题，让你很头疼

### 下载依赖和安装包 并且安装
- 很良心的找到了对应的[版本](https://download.csdn.net/download/zhuimeng0608/10859712),好资源还是要分享一下，真不容易
- 之前装遇到的问题，主要就是版本对不上，以及因为是离线，因为确实一些对应所需版本的离线安装包，系统相关的确实很困扰
- 下载了上面的压缩包之后
```text
tar -zxvf docker1809.tar.gz

sudo yum localinstall -y *
//我这边是一个普通用户，并不是root用户，因而需要用到sudo,因为docker的安装会涉及到非用户目录

```
- 亲测，这样的情况下docker就安装好了
- 接下来就要启动
```text
sudo systemctl enable docker
sudo systemctl start docker

接下来就通过status查看启动情况
sudo systemctl status docker
亲测，能够看到docker启动成功

其实看了一下压缩包内容，很重要的包就是container相关的包，里面有系统所需的一些东西，是在centos7.4 docker18.06安装过程中因缺失而无法继续的坎

```
- 最后可以通过sudo  docker info命令，检测一下docker是否运作
```text
Containers: 0
 Running: 0
 Paused: 0
 Stopped: 0
Images: 1
Server Version: 18.09.0
Storage Driver: overlay2
 Backing Filesystem: xfs
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
containerd version: c4446665cb9c30056f4998ed953e6d4ff22c7c39
runc version: 4fc53a81fb7c994640722ac585fa9ca548971871
init version: fec3683
Security Options:
 seccomp
  Profile: default
Kernel Version: 3.10.0-957.el7.x86_64
Operating System: CentOS Linux 7 (Core)
OSType: linux
Architecture: x86_64
CPUs: 1
Total Memory: 7.62GiB
Name: bogon
ID: Y5U5:NZHE:DE7Z:5S5F:TOO2:JQRQ:NO23:7CJI:ULDP:KMWD:HB6W:WJ6E
Docker Root Dir: /var/lib/docker
Debug Mode (client): false
Debug Mode (server): false
Registry: https://index.docker.io/v1/
Labels:
Experimental: false
Insecure Registries:
 127.0.0.0/8
Live Restore Enabled: false
Product License: Community Engine
```
### 安装docker-compose
- 如果安装了docker，很多情况不仅仅是简单的装几个东西，肯定会需要用来做一些部署，如果需要用到容器编排，肯定就是少不了k8s和docker-compose，那这边介绍一下docker-compose
- 到github上下载一个[安装包release](https://github.com/docker/compose/releases),这边就选取了一个较新稳定版本1.23.2-[docker-compose-Linux-x86_64](https://github.com/docker/compose/releases/download/1.23.2/docker-compose-Linux-x86_64)
- 下载之后
```text
//重命名
mv docker-compose-Linux-x86_64.octet-stream docker-compose
//拷贝
cp docker-compose /usr/local/bin
//授权,可执行权限
sudo chmod +x docker-compose
//最后检验一下
docker-compose -version

```

### 导出导入镜像
- 导出
```text
docker save -o 名称.tar  已有镜像名
```
- 导入：注意箭头不要反了
```text
docker load < 名称.tar
```

### 卸载docker?
- 用yum来获取列表
```text
sudo yum list installed | grep docker
//能够获取已经安装的docker相关的包，那就一个一个删除就可以了
sudo yum -y remove docker-engine.x86_64
//如果需要彻底删除docker，需要执行一下步骤，如果需要重装或者升级保留镜像的，就不需要
 rm -rf /var/lib/docker
```



