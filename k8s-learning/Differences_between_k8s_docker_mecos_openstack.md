> 作为新手，难免会有各种各样的疑问，这么多虚拟化技术到底有什么关联


### openstack --- 管理容器的主流平台,主要用来管理vm
- 云计算**Iaas平台**，主要管理虚拟机和物理机

### docker --- 容器创建和管理的事实标准，主要用来创建和管理容器
- 是一种容器运行时的实现
- 出现容器编排、docker集群、服务发现


### kubernates(k8s) --- 管理的是核心目标对象，容器
- 搭建容器集群和进行容器编排主流的开源项目，有Google支撑
- 适合搭建**Paas平台**
- k8s管理容器 和 openstack管理虚拟机一样


### Mesos --- 通用资源管理平台，管理各种计算资源 cpu memory disk port gpu
- 和yarn是类似的产品，应对于资源的统一协调管理


### kubernates && mesos
- 如果只是用于容器集群管理，Kubernetes 更加合适
- 如果定制需求比较多，或者要搭建大数据平台，架构相对松耦合的 Mesos 显然更加合适
- 两者也可以共同使用
```text
千节点集群，少定制：使用开源 Kubernetes （细粒度设计，契合微服务思想）
万节点集群，多定制：使用 Mesos + Marathon （双层调度好犀利）
万节点集群，IT 能力强：深度定制 Kubernetes （如网易云）
万节点集群，IT 能力强：深入掌握使用 DC/OS （DC/OS 在最基础的 Marathon 和 Mesos 之上添加了很多的组件）
大数据集群：Spark on Mesos （建议只基于容器部署计算部分，数据部分另行部署）


```

### Kubernetes与Mesos + Marathon 容器编排解决方案的对比
- kubernates组件：主节点，工作节点，etcd,apiserver,controller manager,schduler,kubelet
- mesos+marathon组件：mesos master,masos slave,framework,zookeeper,marathon scheduler,docker executor
- mesosphere dcos 是一个高级体系结构~~~~


|项目|kubernates|mesos+marathon|
|:---:|:---:|:---:|
|应用定义|使用Pod，部署和服务的组合来部署应用程序|Marathon将容器调度为在从节点上执行的任务，mesos做资源分配|
|应用的可扩展性|pod--应用程序层，可以在通过声明性指定的部署进行管理时进行缩放，自动或手动|marathon：可以使用Mesos CLI或UI，指定资源，支持自动|
|高可用|pod部署在不同节点上，支持高可用|marathon：使用Zookeeper支持Mesos和Marathon的高可用性。 Zookeeper提供Mesos和Marathon领导者的选举并维护集群状态|
|负载均衡|pod暴露服务，可以在集群内做负载均衡|marathon 主机端口可以映射到多个容器端口|
|应用的自动伸缩|使用部署以声明方式定义|如果其中一个容器发生故障，Marathon会将其重新安排在其他从属节点上。 只有通过社区支持的组件才能使用资源指标进行自动扩展|
|应用的滚动升级和回滚|在deployment中有滚动升级和回滚的策略|marathon：部署支持应用程序的滚动升级。失败的升级可以使用回滚更改的更新部署进行修复|
|健康检查|活跃和准备|marathon：运行状况检查可以指定为针对应用程序的任务运行|
|存储|Kubernetes提供了几种类型的持续卷，支持块或文件|本地持久性卷（测试版）支持有状态的应用程序，如MySQL|
|网络|网络模型是一个扁平的网络，使所有的pod互相通信，平面网络通常作为overlay来实现|网络可以在主机模式或网桥模式下进行配置|
|服务发现|使用环境变量或DNS来找到服务，运行pod时，Kubelet会添加一组环境变量|服务可以通过“命名VIP”发现，它们是与IP和端口关联的DNS记录|
|性能|Kubernetes可扩展到5,000个节点的集群。可以集群联邦来扩展超出此限制|Kubernetes可扩展到5,000个节点的集群。可以集群联邦来扩展超出此限制，10000|

                   