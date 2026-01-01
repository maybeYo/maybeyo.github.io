---
title: kubeadm部署k8s
date: 2021-06-20 14:32:58
tags: 
  - k8s
categories: 
  - k8s
---





kubeadm是官方社区推出的一个用于快速部署kubernetes集群的工具。
<!-- more -->
这个工具能通过两条指令完成一个kubernetes集群的部署：

```shell
# 创建一个 Master 节点
$ kubeadm init

# 将一个 Node 节点加入到当前集群中
$ kubeadm join <Master节点的IP和端口 >
```

## 1. 安装要求

在开始之前，部署Kubernetes集群机器需要满足以下几个条件：

- 一台或多台机器，操作系统 CentOS7.x-86_x64
- 硬件配置：2GB或更多RAM，2个CPU或更多CPU，硬盘30GB或更多
- 集群中所有机器之间网络互通
- 可以访问外网，需要拉取镜像
- 禁止swap分区

## 2. 准备环境



| 角色       | IP            |
| ---------- | ------------- |
| k8s-master | 192.168.81.57 |
| k8s-node1  | 192.168.81.58 |
| k8s-node2  | 192.168.81.59 |

```shell
# 关闭防火墙：
systemctl stop firewalld
systemctl disable firewalld

# 关闭selinux：
sed -i 's/enforcing/disabled/' /etc/selinux/config  # 永久
setenforce 0  # 临时

# 关闭swap：
swapoff -a  # 临时
vim /etc/fstab  # 永久
swapoff /dev/mapper/centos-swap
free -h
# 设置主机名：
hostnamectl set-hostname <hostname>

# 在master添加hosts：
cat >> /etc/hosts << EOF
192.168.81.57 k8s-master
192.168.81.58 k8s-node1
192.168.81.59 k8s-node2
EOF

# 将桥接的IPv4流量传递到iptables的链：
cat > /etc/sysctl.d/k8s.conf << EOF
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1
EOF
sysctl --system  # 生效

# 时间同步：
yum install ntpdate -y
ntpdate time.windows.com
```

## 3. 安装Docker/kubeadm/kubelet【所有节点】

Kubernetes默认CRI（容器运行时）为Docker，因此先安装Docker。

### 3.1 安装Docker

```sh
wget http://mirrors.aliyun.com/repo/Centos-7.repo
wget https://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo -O /etc/yum.repos.d/docker-ce.repo
yum -y install docker-ce
systemctl enable docker && systemctl start docker

# 查看docker版本
[root@localhost ~]# docker version
Client: Docker Engine - Community
 Version:           20.10.5
 API version:       1.41
 Go version:        go1.13.15
 Git commit:        55c4c88
 Built:             Tue Mar  2 20:33:55 2021
 OS/Arch:           linux/amd64
 Context:           default
 Experimental:      true

Server: Docker Engine - Community
 Engine:
  Version:          20.10.5
  API version:      1.41 (minimum version 1.12)
  Go version:       go1.13.15
  Git commit:       363e9a8
  Built:            Tue Mar  2 20:32:17 2021
  OS/Arch:          linux/amd64
  Experimental:     false
 containerd:
  Version:          1.4.4
  GitCommit:        05f951a3781f4f2c1911b05e61c160e9c30eaa8e
 runc:
  Version:          1.0.0-rc93
  GitCommit:        12644e614e25b05da6fd08a38ffa0cfe1903fdec
 docker-init:
  Version:          0.19.0
  GitCommit:        de40ad0

```

配置镜像下载加速器：

```shell
cat > /etc/docker/daemon.json << EOF
{
  "registry-mirrors": ["https://b9pmyelo.mirror.aliyuncs.com"]
}
EOF

systemctl restart docker
docker info
```

### 3.2 添加阿里云YUM软件源

```shell
cat > /etc/yum.repos.d/kubernetes.repo << EOF
[kubernetes]
name=Kubernetes
baseurl=https://mirrors.aliyun.com/kubernetes/yum/repos/kubernetes-el7-x86_64
enabled=1
gpgcheck=0
repo_gpgcheck=0
gpgkey=https://mirrors.aliyun.com/kubernetes/yum/doc/yum-key.gpg https://mirrors.aliyun.com/kubernetes/yum/doc/rpm-package-key.gpg
EOF

```

### 3.2.1 常用管理命令

```shell
# 命令格式：docker image COMMAND

# 指令 描述
# ls        //列出镜像
# build     //构建镜像来自Dockerfile
# history   //查看镜像历史
# inspect   //显示一个或多个镜像详细信息
# pull      //从镜像仓库拉取镜像
# push      //推送一个镜像到镜像仓库
# rm        //移除一个或多个镜像
# prune     //移除没有被标记或者没有被任何容器引用的镜像
# tag       //创建一个引用源镜像标记目标镜像
# save      //保存一个或多个镜像到一个tar归档文件
# load      //加载镜像来自tar归档或标准输入
```

> docker save load使用

```shell
# 将有网络的镜像打包为tar文件 81.57操作
[root@localhost ~]# docker save nginx -o nginx.tar
[root@localhost ~]# ls
anaconda-ks.cfg  nginx.tar
[root@localhost ~]# du -sh nginx.tar 
131M  nginx.tar

# 查看tar包内容
[root@localhost ~]# tar tvf nginx.tar 
-rw-r--r-- 0/0            7731 2021-03-11 08:21 018aec2b4f302b08b4c7274b72bede1fe56ee1f2bcaa06492e3f464e05f1a9a8.json
drwxr-xr-x 0/0               0 2021-03-11 08:21 1f1a90348f908c579d0340f4bac7680918de1fa2f4c4fd2c2145a663d6178064/
-rw-r--r-- 0/0               3 2021-03-11 08:21 1f1a90348f908c579d0340f4bac7680918de1fa2f4c4fd2c2145a663d6178064/VERSION
-rw-r--r-- 0/0             482 2021-03-11 08:21 1f1a90348f908c579d0340f4bac7680918de1fa2f4c4fd2c2145a663d6178064/json
-rw-r--r-- 0/0            4096 2021-03-11 08:21 1f1a90348f908c579d0340f4bac7680918de1fa2f4c4fd2c2145a663d6178064/layer.tar
drwxr-xr-x 0/0               0 2021-03-11 08:21 3ed7719d733be9ef3895d3b6435ba67c07087b06aaef54a4d63ea59ca1ca5c32/
-rw-r--r-- 0/0               3 2021-03-11 08:21 3ed7719d733be9ef3895d3b6435ba67c07087b06aaef54a4d63ea59ca1ca5c32/VERSION
-rw-r--r-- 0/0            1682 2021-03-11 08:21 3ed7719d733be9ef3895d3b6435ba67c07087b06aaef54a4d63ea59ca1ca5c32/json
-rw-r--r-- 0/0            7168 2021-03-11 08:21 3ed7719d733be9ef3895d3b6435ba67c07087b06aaef54a4d63ea59ca1ca5c32/layer.tar
drwxr-xr-x 0/0               0 2021-03-11 08:21 4fc81aabdfa5c3ae98c390eccf8414520a26d6c3aa8974d5fccccf61d889aa04/
-rw-r--r-- 0/0               3 2021-03-11 08:21 4fc81aabdfa5c3ae98c390eccf8414520a26d6c3aa8974d5fccccf61d889aa04/VERSION
-rw-r--r-- 0/0             482 2021-03-11 08:21 4fc81aabdfa5c3ae98c390eccf8414520a26d6c3aa8974d5fccccf61d889aa04/json
-rw-r--r-- 0/0            3584 2021-03-11 08:21 4fc81aabdfa5c3ae98c390eccf8414520a26d6c3aa8974d5fccccf61d889aa04/layer.tar
drwxr-xr-x 0/0               0 2021-03-11 08:21 7822202c2b2274fafc6cf0d948baca257fe26ccb6858084a19ec635e81b210f3/
-rw-r--r-- 0/0               3 2021-03-11 08:21 7822202c2b2274fafc6cf0d948baca257fe26ccb6858084a19ec635e81b210f3/VERSION
-rw-r--r-- 0/0             482 2021-03-11 08:21 7822202c2b2274fafc6cf0d948baca257fe26ccb6858084a19ec635e81b210f3/json
-rw-r--r-- 0/0            3072 2021-03-11 08:21 7822202c2b2274fafc6cf0d948baca257fe26ccb6858084a19ec635e81b210f3/layer.tar
drwxr-xr-x 0/0               0 2021-03-11 08:21 8b9e24c9de24a93a1a2da83ab6830ba2ac2914fb5c3af42bc3a8c198640e1299/
-rw-r--r-- 0/0               3 2021-03-11 08:21 8b9e24c9de24a93a1a2da83ab6830ba2ac2914fb5c3af42bc3a8c198640e1299/VERSION
-rw-r--r-- 0/0             482 2021-03-11 08:21 8b9e24c9de24a93a1a2da83ab6830ba2ac2914fb5c3af42bc3a8c198640e1299/json
-rw-r--r-- 0/0        64807936 2021-03-11 08:21 8b9e24c9de24a93a1a2da83ab6830ba2ac2914fb5c3af42bc3a8c198640e1299/layer.tar
drwxr-xr-x 0/0               0 2021-03-11 08:21 e86c0242e63168af8ce7e8640b4f884c500a003fed9ccfedd98f682c5026daf4/
-rw-r--r-- 0/0               3 2021-03-11 08:21 e86c0242e63168af8ce7e8640b4f884c500a003fed9ccfedd98f682c5026daf4/VERSION
-rw-r--r-- 0/0             406 2021-03-11 08:21 e86c0242e63168af8ce7e8640b4f884c500a003fed9ccfedd98f682c5026daf4/json
-rw-r--r-- 0/0        72491008 2021-03-11 08:21 e86c0242e63168af8ce7e8640b4f884c500a003fed9ccfedd98f682c5026daf4/layer.tar
-rw-r--r-- 0/0             586 1970-01-01 08:00 manifest.json
-rw-r--r-- 0/0              88 1970-01-01 08:00 repositories

# 传到没有网络的主机
[root@localhost ~]# scp nginx.tar 192.168.81.58:/root

# 81.58操作导入tar包到本地镜像
[root@localhost ~]# docker load -i  nginx.tar 

[root@localhost ~]# docker images 
REPOSITORY   TAG       IMAGE ID       CREATED        SIZE
nginx        latest    018aec2b4f30   26 hours ago   133MB

```

```shell
# 启动nginx镜像映射IP
[root@localhost ~]# docker run -d -p 8080:80 nginx
38f999f57ffa1c1ab6799eab45323152564a943c00081a3f98838f5ef29fca21
```

### 3.3 安装kubeadm，kubelet和kubectl

由于版本更新频繁，这里指定版本号部署：

```shell
# 所有节点操作
yum install -y kubelet-1.20.0 kubeadm-1.20.0 kubectl-1.20.0
systemctl enable kubelet
```

## 4. 部署Kubernetes Master

<https://kubernetes.io/zh/docs/reference/setup-tools/kubeadm/kubeadm-init/#config-file>

<https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/create-cluster-kubeadm/#initializing-your-control-plane-node>

在192.168.81.57（Master）执行。

```shell
kubeadm init \
  --apiserver-advertise-address=192.168.81.57 \
  --image-repository registry.aliyuncs.com/google_containers \
  --kubernetes-version v1.20.0 \
  --service-cidr=10.96.0.0/12 \
  --pod-network-cidr=10.244.0.0/16 \
  --ignore-preflight-errors=all
```

- --apiserver-advertise-address 集群通告地址
- --image-repository  由于默认拉取镜像地址k8s.gcr.io国内无法访问，这里指定阿里云镜像仓库地址
- --kubernetes-version K8s版本，与上面安装的一致
- --service-cidr 集群内部虚拟网络，Pod统一访问入口
- --pod-network-cidr Pod网络，，与下面部署的CNI网络组件yaml中保持一致

或者使用配置文件引导：

```shell
vi kubeadm.conf
apiVersion: kubeadm.k8s.io/v1beta2
kind: ClusterConfiguration
kubernetesVersion: v1.20.0
imageRepository: registry.aliyuncs.com/google_containers 
networking:
  podSubnet: 10.244.0.0/16 
  serviceSubnet: 10.96.0.0/12 

kubeadm init --config kubeadm.conf --ignore-preflight-errors=all  
```

拷贝kubectl使用的连接k8s认证文件到默认路径：

```bash
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config

[root@k8s-master ~]# ll .kube/config 
-rw------- 1 root root 5569 3月  12 11:58 .kube/config

```

```shell
kubectl get nodes
NAME               STATUS     ROLES            AGE   VERSION
localhost.localdomain   NotReady   control-plane,master   20s   v1.20.0
```

## 5. 加入Kubernetes Node

在192.168.81.58/59（Node）执行。

向集群添加新节点，执行在kubeadm init输出的kubeadm join命令：

```sh
kubeadm join 192.168.81.57:6443 --token tqxl1u.8vwoeza99jxzgoi0 \
    --discovery-token-ca-cert-hash sha256:48321ed1651b239cee9dd3b482a24e9deee1e48c7ca3b34cfa429524538275ba 
```

默认token有效期为24小时，当过期之后，该token就不可用了。这时就需要重新创建token，可以直接使用命令快捷生成：

```shell
kubeadm token create --print-join-command
```

<https://kubernetes.io/docs/reference/setup-tools/kubeadm/kubeadm-join/>

## 6. 部署容器网络（CNI）

<https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/create-cluster-kubeadm/#pod-network>

注意：只需要部署下面其中一个，推荐Calico。

Calico是一个纯三层的数据中心网络方案，Calico支持广泛的平台，包括Kubernetes、OpenStack等。

Calico 在每一个计算节点利用 Linux Kernel 实现了一个高效的虚拟路由器（ vRouter） 来负责数据转发，而每个 vRouter 通过 BGP 协议负责把自己上运行的 workload 的路由信息向整个 Calico 网络内传播。

此外，Calico  项目还实现了 Kubernetes 网络策略，提供ACL功能。

<https://docs.projectcalico.org/getting-started/kubernetes/quickstart>

```shell
wget https://docs.projectcalico.org/manifests/calico.yaml

# 修改yaml以下内容 IP地址段与上述init地址段保持一致
[root@k8s-master ~]# vim calico.yaml
            - name: CALICO_IPV4POOL_CIDR
              value: "10.244.0.0/16"

```

下载完后还需要修改里面定义Pod网络（CALICO_IPV4POOL_CIDR），与前面kubeadm init指定的一样

修改完后应用清单：

```shell
kubectl apply -f calico.yaml
kubectl get pods -n kube-system
```

## 7. 测试kubernetes集群

- 验证Pod工作
- 验证Pod网络通信
- 验证DNS解析

在Kubernetes集群中创建一个pod，验证是否正常运行：

```shell
kubectl create deployment nginx --image=nginx
kubectl expose deployment nginx --port=80 --type=NodePort
[root@k8s-master ~]# kubectl expose deployment web --port=80 --target-port=80 --type=NodePort

kubectl get pod,svc


# 查看 master 组件状态
[root@k8s-master ~]# kubectl get cs
Warning: v1 ComponentStatus is deprecated in v1.19+
NAME                 STATUS      MESSAGE                                                                                       ERROR
scheduler            Unhealthy   Get "http://127.0.0.1:10251/healthz": dial tcp 127.0.0.1:10251: connect: connection refused   
controller-manager   Unhealthy   Get "http://127.0.0.1:10252/healthz": dial tcp 127.0.0.1:10252: connect: connection refused   
etcd-0               Healthy     {"health":"true"}  

查看 master 组件不健康，估计版本小bug，不影响使用，如果要修复：
打开两个文件：
/etc/kubernetes/manifests/kube-controller-manager.yaml
/etc/kubernetes/manifests/kube-scheduler.yaml
#注释掉 --port=0，开启本地非安全端口，然后systemctl restart kubelet
```

```shell
# 访问地址：http://NodeIP:Port  
```

## 8. 部署 Dashboard

```shell
wget https://raw.githubusercontent.com/kubernetes/dashboard/v2.0.3/aio/deploy/recommended.yaml
```

默认Dashboard只能集群内部访问，修改Service为NodePort类型，暴露到外部：

```shell
vi recommended.yaml
...
kind: Service
apiVersion: v1
metadata:
  labels:
    k8s-app: kubernetes-dashboard
  name: kubernetes-dashboard
  namespace: kubernetes-dashboard
spec:
  ports:
    - port: 443
      targetPort: 8443
      nodePort: 30001
  selector:
    k8s-app: kubernetes-dashboard
  type: NodePort
...

kubectl apply -f recommended.yaml
kubectl get pods -n kubernetes-dashboard
NAME                                         READY   STATUS    RESTARTS   AGE
dashboard-metrics-scraper-6b4884c9d5-gl8nr   1/1     Running   0          13m
kubernetes-dashboard-7f99b75bf4-89cds        1/1     Running   0          13m$ vi recommended.yaml...kind: ServiceapiVersion: v1metadata:  labels:    k8s-app: kubernetes-dashboard  name: kubernetes-dashboard  namespace: kubernetes-dashboardspec:  ports:    - port: 443      targetPort: 8443      nodePort: 30001  selector:    k8s-app: kubernetes-dashboard  type: NodePort...$ kubectl apply -f recommended.yaml$ kubectl get pods -n kubernetes-dashboardNAME                                         READY   STATUS    RESTARTS   AGEdashboard-metrics-scraper-6b4884c9d5-gl8nr   1/1     Running   0          13mkubernetes-dashboard-7f99b75bf4-89cds        1/1     Running   0          13m
```

访问地址：
[https://NodeIP:30001](https://NodeIP:30001)

创建service account并绑定默认cluster-admin管理员集群角色：

```shell
# 创建用户
$ kubectl create serviceaccount dashboard-admin -n kube-system
# 用户授权
$ kubectl create clusterrolebinding dashboard-admin --clusterrole=cluster-admin --serviceaccount=kube-system:dashboard-admin
# 获取用户Token
$ kubectl describe secrets -n kube-system $(kubectl -n kube-system get secret | awk '/dashboard-admin/{print $1}')
```

使用输出的token登录Dashboard。

## 9. 切换容器引擎为Containerd

<https://kubernetes.io/zh/docs/setup/production-environment/container-runtimes/#containerd>

1、配置先决条件

```shell
cat <<EOF | sudo tee /etc/modules-load.d/containerd.conf
overlay
br_netfilter
EOF

sudo modprobe overlay
sudo modprobe br_netfilter

# 设置必需的 sysctl 参数，这些参数在重新启动后仍然存在。
cat <<EOF | sudo tee /etc/sysctl.d/99-kubernetes-cri.conf
net.bridge.bridge-nf-call-iptables  = 1
net.ipv4.ip_forward                 = 1
net.bridge.bridge-nf-call-ip6tables = 1
EOF

# Apply sysctl params without reboot
sudo sysctl --system
```

2、安装containerd

```shell
# 安装 containerd
## 设置仓库
### 安装所需包
yum install -y yum-utils device-mapper-persistent-data lvm2

# 添加 docker 仓库
yum-config-manager \
    --add-repo \
    https://download.docker.com/linux/centos/docker-ce.repo

# 添加 contoinerd
yum update -y && sudo yum install -y containerd.io

# 配置 contoinerd
mkdir -p /etc/containerd
containerd config default | sudo tee /etc/containerd/config.toml

# 重启 contoinerd
systemctl restart containerd
```

3、修改配置文件

```shell
vi /etc/containerd/config.toml
   [plugins."io.containerd.grpc.v1.cri"]
      sandbox_image = "registry.aliyuncs.com/google_containers/pause:3.2"  
         ...        # 改为国内地址
         [plugins."io.containerd.grpc.v1.cri".containerd.runtimes.runc.options]
             SystemdCgroup = true
          ...       # Cgroup驱动
        [plugins."io.containerd.grpc.v1.cri".registry.mirrors."docker.io"]
          endpoint = ["https://b9pmyelo.mirror.aliyuncs.com"]
          # 改为阿里云加速器

```

4、配置kubelet使用containerd

```shell
vi /etc/sysconfig/kubelet 
KUBELET_EXTRA_ARGS=--container-runtime=remote --container-runtime-endpoint=unix:///run/containerd/containerd.sock --cgroup-driver=systemd

systemctl stop docker
systemctl restart kubelet
journalctl -u kubelet 
```

5、验证

```shell
kubectl get node -o wide

k8s-node1  xxx  containerd://1.4.4
```
