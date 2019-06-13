> 苦苦折腾了两天，没有什么基础的知识，硬是要装上所有docker镜像和k8s也是比较愚蠢，不过幸好能装上了
> 本来以为mac docker for Mac 会很容易，像网上所描述的那样就能启动k8s，结果还是告诉我，不要太天真，很伤心
> 当然，或许是因为版本、网络各方面的因素，我不能够很顺利的进行安装，我还是把我所看到的方法进行总结，可能有幸运儿，能一下子就成功呢

### 环境说明
```text
docker Engine: 18.09.2
docker Kubernetes: v1.10.11
自己装 k8s：
{
  "major": "1",
  "minor": "14",
  "gitVersion": "v1.14.2",
  "gitCommit": "66049e3b21efe110454d67df4fa62b08ea79a19b",
  "gitTreeState": "clean",
  "buildDate": "2019-05-16T16:14:56Z",
  "goVersion": "go1.12.5",
  "compiler": "gc",
  "platform": "linux/amd64"
}
minikube version: v1.1.0
```


### docker for mac
#### 阶段一
- 打开desktop的preference ，到kubernates的标签页，然后看着是不是要自动启动等的选项，我们可以先选择一个，然后执行apply&restart
- 以上是步骤说明，很简单，但是如果不存在被墙的问题，不如国外，那就是很顺利，右下角会有kubernates is running的绿色小圆圈，可是国内就是 kubernates is starting ,然后没有下文了
- 原因就是下载资源连接不上，网上说是不稳定，我就觉得可能就是连不上了，你能翻墙当然可以，就是慢一点，可是很苦逼，最近翻墙的软件都被封了
#### 阶段二
- 阶段一失败了，那有些博客就说，可以自己从docker hub上把需要的镜像全都下下来，然后更新一下docker images，然后重启一下，就可以了，所以听上去很简单，就继续尝试
```text
#先下了一个阿里的镜像，上面是kubernates需要的镜像
git clone https://github.com/AliyunContainerService/k8s-for-docker-desktop
cd k8s-for-docker-desktop
./load_images.sh

#刷新一下镜像，结果是全都有了
k8s.gcr.io/kube-proxy                   v1.14.1             20a2d7035165        2 months ago        82.1MB
k8s.gcr.io/kube-apiserver               v1.14.1             cfaa4ad74c37        2 months ago        210MB
k8s.gcr.io/kube-scheduler               v1.14.1             8931473d5bdb        2 months ago        81.6MB
k8s.gcr.io/kube-controller-manager      v1.14.1             efb3887b411d        2 months ago        158MB
gcr.io/kubernetes-helm/tiller           v2.13.1             cb5aea7d0466        2 months ago        82.1MB
k8s.gcr.io/coredns                      1.3.1               eb516548c180        5 months ago        40.3MB
docker/kube-compose-controller          v0.4.18             47df7579c5fc        5 months ago        30.6MB
docker/kube-compose-api-server          v0.4.18             e73645df5dc6        5 months ago        47.8MB
k8s.gcr.io/kubernetes-dashboard-amd64   v1.10.1             f9aed6605b81        5 months ago        122MB
k8s.gcr.io/etcd                         3.3.10              2c4adeb21b4f        6 months ago        258MB
k8s.gcr.io/pause                        3.1                 da86e6ba6ca1        17 months ago       742kB
quay.io/coreos/hyperkube                v1.7.6_coreos.0     2faf6f7a322f        21 months ago       699MB
```
- 看到上面的步骤都很正常，那应该没有什么问题了，然后就跟随步骤重启，最终依旧是kubernates is starting。。。。
- 查看了网上的评论，也不是我一个人有这样的问题，也是按照步骤下载都成功了，却还是启动不起来，甚至还听了他们的话去重启电脑，也是徒劳
- 最终，在这个docker desktop上面折腾看来是没用了，最后就放弃去minikube了


### minikube
- 有那么好的mac条件，不用desktop是非常遗憾的，可是翻不了墙也没有办法
```text
流程
kubectl   ----有
docker (for Mac) -----有
minikube
virtualbox
```
#### 阶段一
- 由于minikube是一个小型版本，需要你自己装比较多的东西
- 安装kubectl : 装过docker就不用装了，已经带好了，本来就是给默认的kubernates来使用的，高兴的话，可以看一下版本
```text
bogon:~ xxxx$ kubectl version
Client Version: version.Info{Major:"1", Minor:"10", GitVersion:"v1.10.11", GitCommit:"637c7e288581ee40ab4ca210618a89a555b6e7e9", GitTreeState:"clean", BuildDate:"2018-11-26T14:38:32Z", GoVersion:"go1.9.3", Compiler:"gc", Platform:"darwin/amd64"}
Server Version: version.Info{Major:"1", Minor:"14", GitVersion:"v1.14.2", GitCommit:"66049e3b21efe110454d67df4fa62b08ea79a19b", GitTreeState:"clean", BuildDate:"2019-05-16T16:14:56Z", GoVersion:"go1.12.5", Compiler:"gc", Platform:"linux/amd64"}
```
- 安装minikube:brew cask install minikube
```text
我这里至少brew还是可以用
如果不行的话；
curl -Lo minikube https://storage.googleapis.com/minikube/releases/v0.29.0/minikube-darwin-amd64 && chmod +x minikube && sudo cp minikube /usr/local/bin/ && rm minikube

当然很不幸的是，https://storage.googleapis.com这个网站不知道是慢还是什么，我在浏览器也没办法访问，所以意思就是下不了
```
#### 阶段二
- 接下来就是启动阶段了，minikube start，有的教程很没有责任，什么也不说，其实启动集群，初始化服务，这个过程会下载虚拟机镜像，启动virtualbox的虚拟机（默认），可是看到上面的流程我们还没有装
- 安装virtulabox：但是如果你没有被墙，minikube start获取会给你默认去下载并启动，可是现在我这边不成立，我自己下试试
- brew cask install virtulabox，很凄凉，57.7%永远的卡住了，那么就只能放弃了
#### 阶段三
- 安装另一个虚拟器（hyperkit），这个流畅的
```text
$ curl -LO https://github.com/kubernetes/minikube/releases/download/v0.24.1/docker-machine-driver-hyperkit

$ chmod +x docker-machine-driver-hyperkit

$ sudo mv docker-machine-driver-hyperkit /usr/local/bin/

$ sudo chown root:wheel /usr/local/bin/docker-machine-driver-hyperkit

$ sudo chmod u+s /usr/local/bin/docker-machine-driver-hyperkit

```
- 安装好了，没有问题，我们可以都检查一遍版本
```text
docker --version                # Docker version 18.09.6-ce
docker-compose --version        # docker-compose version 1.23.2, build 1110ad01
docker-machine --version        # docker-machine version 0.16.1
minikube version                # minikube version: v1.1.0
kubectl version --client 
```
- 以上都没有问题后，终于可以启动了
```text
 minikube start --vm-driver=hyperkit --alsologtostderr --v 10
 一旦驱动设置之后，就不要修改了
```
- minikube ip 很开心有了 192.168.64.2
- 然而这还没有结束，我们想看到dashboard
- minikube dashboard 
```text
Error from server (Forbidden): Forbidden (user=system:anonymous, verb=get, resource=nodes, subresource=proxy)
有报错，匿名系统用户没有权限访问
```
#### 阶段四
- 这个问题百度一下很好解决，无非就是你当前用户没有权限访问一些目录，那我们可以建立用户，和容器中的用户绑定，可以是namespace级别的，也可是是cluster级别的
- 我们为了迫切的看一下dashboard，我们建立一个集群级别的用户绑定
```text
kubectl create clusterrolebinding system:anonymous   --clusterrole=cluster-admin   --user=system:anonymous
其中这个cluster-admin的用户是集群里面的一个用户，如果你想绑定别的，可以先查看用户和权限，将当前用户和里面的用户绑定
```
- [角色的绑定和配置具体查看](https://www.jianshu.com/p/9991f189495f)
- 再次运行minikube dashboard就可以跳出默认页面了，这就算基本成功了


[参考1](https://www.jianshu.com/p/18441c7434a6)
[参考2](https://www.jianshu.com/p/3b419cc7d290)
[参考3****](https://cloud.tencent.com/developer/article/1015023)
[参考4](https://www.jianshu.com/p/74957f08646b)
[参考5](https://yq.aliyun.com/articles/508460)
[参考6](https://stackoverflow.com/questions/54656963/forbidden-user-cannot-get-path-not-anonymous-user)
