---
title: k8s集群加入与删除
date: 2021-08-02 16:22:11
tags:
  - Linux
  - k8s
categories: 
  - k8s
---

k8s集群加入与删除

<!--more-->

# 1.添加节点

>通过kubeadm初始化后，都会提供node加入的token:
>默认token的有效期为24小时，当过期之后，该token就不可用了。
```shell
# master 上重新生成新的 token
[root@k8s-master ~]# kubeadm token create --print-join-command
kubeadm join 192.168.81.57:6443 --token xv27mz.zx9qvxzr1n9ver8b     --discovery-token-ca-cert-hash sha256:e79022dddd20ebaa3304fe62856393cb58a5b5b6e42e51333224e1841bbf49eb 
[root@k8s-master ~]# kubeadm token list
TOKEN                     TTL         EXPIRES                     USAGES                   DESCRIPTION                                                EXTRA GROUPS
xv27mz.zx9qvxzr1n9ver8b   23h         2021-06-17T09:26:38+08:00   authentication,signing   <none>                                                     system:bootstrappers:kubeadm:default-node-token

```


```shel
# 新节点运行token
[root@k8s-node2 ~]# kubeadm join 192.168.81.57:6443 --token xv27mz.zx9qvxzr1n9ver8b     --discovery-token-ca-cert-hash sha256:e79022dddd20ebaa3304fe62856393cb58a5b5b6e42e51333224e1841bbf49eb 
```


```shell
# master 查看新节点的是否加入
[root@k8s-master ~]# kubectl get node 
NAME         STATUS     ROLES                  AGE   VERSION
k8s-master   Ready      control-plane,master   70d   v1.20.0
k8s-node1    Ready      <none>                 70d   v1.20.0
k8s-node2    NotReady   <none>                 3s    v1.20.0
[root@k8s-master ~]# systemctl restart kubelet 
[root@k8s-master ~]# kubectl get node 
NAME         STATUS   ROLES                  AGE   VERSION
k8s-master   Ready    control-plane,master   70d   v1.20.0
k8s-node1    Ready    <none>                 70d   v1.20.0
k8s-node2    Ready    <none>                 12s   v1.20.0

```

>node节点重新加入集群操作【node节点操作】


node节点要重新加入集群，需要重置集群状态，命令：kubeadm reset，回车后输入y即可
```shell
kubeadm reset
```
重新加入集群
```shell
kubeadm join 192.168.81.57:6443 --token xv27mz.zx9qvxzr1n9ver8b     --discovery-token-ca-cert-hash sha256:e79022dddd20ebaa3304fe62856393cb58a5b5b6e42e51333224e1841bbf49eb 
```



# 2.移除节点

> k8s集群 移除节点操作

1.确认需要移除的节点上面没有部署我们所需要的资源

```shell
kubectl get pod -A -o wide |grep -w "node名" 
```
2.设置该节点为不可调度(不分配新的资源到该节点上) (drain命令已经会自动把node设置为不可调度，所以可以省略执行cordon命令)
```shell
kubectl cordon node
```

3.确认完成后，先排空节点上的pod(每个节点上面都会运行一些系统自带的pod) (daemonset不会被排出节点)
```shell
kubectl drain node --ignore-daemonsets --force
```

4.排空pod之后，便可以删除节点了

```shell
kubectl delete node node名
```



参考链接：[https://blog.csdn.net/yexusanye/](https://blog.csdn.net/yexusanye/article/details/117947399)

