[中文文档](http://docs.kubernetes.org.cn/618.html)
- kubectl cluster-info
- kubectl get nodes
- kubectl apply -f kube-proxy-rbac.yaml
- kubectl get namespace
- kubectl get posts --namespace kube-system
- kubectl config get-contexts
- kubectl get pods

```text
kubectl get role -n kube-system
查看kube-system namespace下的所有role

kubectl get role <role-name> -n kube-system -o yaml
查看某个role定义的资源权限

kubectl get rolebinding -n kube-system
查看kube-system namespace下所有的rolebinding

kubectl get rolebinding <rolebind-name> -n kube-system -o yaml
查看kube-system namespace下的某个rolebinding详细信息（绑定的Role和subject）

kubectl get clusterrole
查看集群所有的clusterrole

kubectl get clusterrole <clusterrole-name> -o yaml
查看某个clusterrole定义的资源权限详细信息

kubectl get clusterrolebinding
查看所有的clusterrolebinding

kubectl get clusterrolebinding <clusterrolebinding-name> -o yaml
查看某一clusterrolebinding的详细信息


有两个kubectl命令可以用于在命名空间内或者整个集群内授予角色。


kubectl create rolebinding
在某一特定名字空间内授予Role或者ClusterRole。示例如下：
a) 在名为”acme”的名字空间中将admin ClusterRole授予用户”bob”：
kubectl create rolebinding bob-admin-binding --clusterrole=admin --user=bob --namespace=acme
b) 在名为”acme”的名字空间中将view ClusterRole授予服务账户”myapp”：

kubectl create rolebinding myapp-view-binding --clusterrole=view --serviceaccount=acme:myapp --namespace=acme
kubectl create clusterrolebinding
在整个集群中授予ClusterRole，包括所有名字空间。示例如下：
a) 在整个集群范围内将cluster-admin ClusterRole授予用户”root”：
kubectl create clusterrolebinding root-cluster-admin-binding --clusterrole=cluster-admin --user=root
b) 在整个集群范围内将system:node ClusterRole授予用户”kubelet”：
kubectl create clusterrolebinding kubelet-node-binding --clusterrole=system:node --user=kubelet
c) 在整个集群范围内将view ClusterRole授予名字空间”acme”内的服务账户”myapp”：
kubectl create clusterrolebinding myapp-view-binding --clusterrole=view --serviceaccount=acme:myapp

```