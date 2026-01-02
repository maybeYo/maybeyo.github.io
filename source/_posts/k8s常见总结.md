---
title: k8s常见总结2
date: 2021-07-17 11:48:09
tags: 
  - k8s
categories: 
  - k8s

---

k8s常见总结2

<!--more-->

## 1. k8s是什么？

​Kubernetes是一个开源容器管理工具，负责容器部署，容器扩缩容以及负载平衡。作为Google的创意之作，它提供了出色的社区，并与所有云提供商合作。因此，我们可以说Kubernetes不是一个容器化平台，而是一个多容器管理解决方案。

## 2. 容器和主机部署应用的区别是什么？

​容器的中心思想就是秒级启动；一次封装、到处运行；这是主机部署应用无法达到的效果，但同时也更应该注重容器的数据持久化问题。另外，容器部署可以将各个服务进行隔离，互不影响，这也是容器的另一个核心概念。

​主机上部署应用程序。这种架构将具有操作系统，然后操作系统将具有内核，该内核将在应用程序所需的操作系统上安装各种库。因此，在这种框架中，您可以拥有n个应用程序，并且所有应用程序将共享该操作系统中存在的库，而在容器中部署应用程序时，体系结构则略有不同。

这种架构将有一个内核，这是唯一一个在所有应用程序之间唯一共同的东西。因此，如果有一个需要Java的特定应用程序，那么我们将获得访问Java的特定应用程序，如果有另一个需要Python的应用程序，则只有该特定应用程序才能访问Python。

​容器化的，这块与其他应用程序隔离。因此，应用程序具有与系统其余部分隔离的必要库和二进制文件，并且不能被任何其他应用程序侵占。

## 3. Kubernetes如何简化容器化部署？

​由于典型应用程序将具有跨多个主机运行的容器集群，因此所有这些容器都需要相互通信。因此，要做到这一点，你需要一些能够负载平衡，扩展和监控容器的东西。由于Kubernetes与云无关并且可以在任何公共/私有提供商上运行，因此必须是您简化容器化部署的选择。

## 4. Kubernetes的集群了解

Kubernetes背后的基础是我们可以实施所需的状态管理，我的意思是我们可以提供特定配置的集群服务，并且集群服务将在基础架构中运行并运行该配置。

​因此，部署文件将具有提供给集群服务所需的所有配置。现在，部署文件将被提供给API，然后由集群服务决定如何在环境中安排这些pod，并确保正确运行的pod数量。

​因此，位于服务前面的API，工作节点和节点运行的Kubelet进程，共同构成了Kubernetes集群。

## 5. 什么是Google容器引擎？

​Google Container Engine（GKE）是Docker容器和集群的开源管理平台。这个基于Kubernetes的引擎仅支持在Google的公共云服务中运行的群集。

## 6. 什么是Minikube？

​Minikube是一种工具，可以在本地轻松运行Kubernetes。这将在虚拟机中运行单节点Kubernetes群集。

## 7. 什么是Kubectl？

​Kubectl是一个平台，您可以使用该平台将命令传递给集群。因此，它基本上为CLI提供了针对Kubernetes集群运行命令的方法，以及创建和管理Kubernetes组件的各种方法。

​这是一个代理服务，它在每个节点上运行，并使从服务器与主服务器通信。因此，Kubelet处理PodSpec中提供给它的容器的描述，并确保PodSpec中描述的容器运行正常。

## 8. Kubernetes Architecture的不同组件有哪些？

​Kubernetes Architecture主要有两个组件 - 主节点和工作节点。如下图所示，master和worker节点中包含许多内置组件。主节点具有kube-controller-manager，kube-apiserver，kube-scheduler等。而工作节点具有在每个节点上运行的kubelet和kube-proxy。

## 9 .你对 Kube-proxy 有什么了解？

​Kube-proxy可以在每个节点上运行，并且可以跨后端网络服务进行简单的TCP / UDP数据包转发。基本上，它是一个网络代理，它反映了每个节点上Kubernetes API中配置的服务。因此，Docker可链接的兼容环境变量提供由代理打开的群集IP和端口。

## 10. Kubernetes中主节点的工作情况？

​Kubernetes master控制容器存在的节点和节点内部。现在，这些单独的容器包含在容器内部和每个容器内部，您可以根据配置和要求拥有不同数量的容器。因此，如果必须部署pod，则可以使用用户界面或命令行界面部署它们。然后，在节点上调度这些pod，并根据资源需求，将pod分配给这些节点。kube-apiserver确保在Kubernetes节点和主组件之间建立通信。

## 11. kube-apiserver 和 kube-scheduler 的作用是什么？

​`kube-apiserver`遵循横向扩展架构，是主节点控制面板的前端。这将公开Kubernetes主节点组件的所有API，并负责在Kubernetes节点和Kubernetes主组件之间建立通信。

​`kube-scheduler`负责工作节点上工作负载的分配和管理。因此，它根据资源需求选择最合适的节点来运行未调度的pod，并跟踪资源利用率。它确保不在已满的节点上调度工作负载。

## 12. kubernetes控制管理器吗？

​多个控制器进程在主节点上运行，但是一起编译为单个进程运行，即Kubernetes控制器管理器。因此，Controller Manager是一个嵌入控制器并执行命名空间创建和垃圾收集的守护程序。它拥有责任并与API服务器通信以管理端点。

## 13. 什么是ETCD？

​Etcd是用Go编程语言编写的，是一个分布式键值存储，用于协调分布式工作。因此，Etcd存储Kubernetes集群的配置数据，表示在任何给定时间点的集群状态。

## 14. Kubernetes的负载均衡器了解

​负载均衡器是暴露服务的最常见和标准方式之一。根据工作环境使用两种类型的负载均衡器，即*内部负载均衡器*或*外部负载均衡器*。内部负载均衡器自动平衡负载并使用所需配置分配容器，而外部负载均衡器将流量从外部负载引导至后端容器。

## 15. Ingress网络，它是如何工作的？

​Ingress网络是一组规则，充当Kubernetes集群的入口点。这允许入站连接，可以将其配置为通过可访问的URL，负载平衡流量或通过提供基于名称的虚拟主机从外部提供服务。因此，Ingress是一个API对象，通常通过HTTP管理集群中服务的外部访问，是暴露服务的最有效方式。

## 16. Replica Set 和 Replication Controller之间有什么区别？

​`Replica Set`和 `Replication Controller`几乎完全相同。它们都确保在任何给定时间运行指定数量的pod副本。不同之处在于复制pod使用的选择器。Replica Set使用基于集合的选择器，而Replication Controller使用基于权限的选择器。

- Equity-Based选择器：这种类型的选择器允许按标签键和值进行过滤。因此，在外行术语中，基于Equity的选择器将仅查找与标签具有完全相同短语的pod。 示例：假设您的标签键表示app = nginx，那么，使用此选择器，您只能查找标签应用程序等于nginx的那些pod。
- Selector-Based选择器：此类型的选择器允许根据一组值过滤键。因此，换句话说，基于Selector的选择器将查找已在集合中提及其标签的pod。 示例：假设您的标签键在（nginx，NPSApache）中显示应用程序。然后，使用此选择器，如果您的应用程序等于任何nginx，NPS或Apache，则选择器将其视为真实结果。

## 17. K8s架构的组成是什么

![K8S架构图](/images/k8s常见总结2.assets/K8S架构图.png)

- 主节点主要用于暴露API，调度部署和节点的管理；
- 计算节点运行一个容器运行环境，一般是docker环境（类似docker环境的还有rkt），同时运行一个K8s的代理（kubelet）用于和master通信。计算节点也会运行一些额外的组件，像记录日志，节点监控，服务发现等等。计算节点是k8s集群中真正工作的节点。

Master节点：

- Kubectl：客户端命令行工具，作为整个K8s集群的操作入口；
- Api Server：在K8s架构中承担的是“桥梁”的角色，作为资源操作的唯一入口，它提供了认证、授权、访问控制、API注册和发现等机制。客户端与k8s群集及K8s内部组件的通信，都要通过Api Server这个组件；
- Controller-manager：负责维护群集的状态，比如故障检测、自动扩展、滚动更新等；
- Scheduler：负责资源的调度，按照预定的调度策略将pod调度到相应的node节点上；
- Etcd：担任数据中心的角色，保存了整个群集的状态；

Node节点：

- Kubelet：负责维护容器的生命周期，同时也负责Volume和网络的管理，一般运行在所有的节点，是Node节点的代理，当Scheduler确定某个node上运行pod之后，会将pod的具体信息（image，volume）等发送给该节点的kubelet，kubelet根据这些信息创建和运行容器，并向master返回运行状态。（自动修复功能：如果某个节点中的容器宕机，它会尝试重启该容器，若重启无效，则会将该pod杀死，然后重新创建一个容器）；
- Kube-proxy：Service在逻辑上代表了后端的多个pod。负责为Service提供cluster内部的服务发现和负载均衡（外界通过Service访问pod提供的服务时，Service接收到的请求后就是通过kube-proxy来转发到pod上的）；
- container-runtime：是负责管理运行容器的软件，比如docker
- Pod：是k8s集群里面最小的单位。每个pod里边可以运行一个或多个container（容器），如果一个pod中有两个container，那么container的USR（用户）、MNT（挂载点）、PID（进程号）是相互隔离的，UTS（主机名和域名）、IPC（消息队列）、NET（网络栈）是相互共享的。

## 18. kubenetes针对pod资源对象的健康监测机制

K8s中对于pod资源对象的健康状态检测，提供了三类probe（探针）来执行对pod的健康监测：

1） livenessProbe探针

可以根据用户自定义规则来判定pod是否健康，如果livenessProbe探针探测到容器不健康，则kubelet会根据其重启策略来决定是否重启，初始探测状态为健康状态直到探测失败。如果一个容器不包含livenessProbe探针，则kubelet会认为容器的livenessProbe探针的返回值永远成功。

2） ReadinessProbe探针

同样是可以根据用户自定义规则来判断pod是否健康，如果探测失败，控制器会将此pod从对应service的endpoint列表中移除，从此不再将任何请求调度到此Pod上，直到下次探测成功。初始探测为失败状态，直到探测成功后，将pod加入到service的endpoint列表中。

3） startupProbe探针

启动检查机制，应用一些启动缓慢的业务，避免业务长时间启动而被上面两类探针kill掉，这个问题也可以换另一种方式解决，就是定义上面两类探针机制时，初始化时间定义的长一些即可。

探针检查支持以下参数设置：

- initialDelaySeconds：初始第一次探测间隔，用于应用启动的时间，防止应用还没启动而健康检查失败
- periodSeconds：检查间隔，多久执行probe检查，默认为10s；
- timeoutSeconds：检查超时时长，探测应用timeout后为失败；
- successThreshold：成功探测阈值，表示探测多少次为健康正常，默认探测1次。

探针支持分探测方案：

1）.通过执行命令的方式来检查服务是否正常，比如使用cat命令查看pod中的某个重要配置文件是否存在，若存在，则表示pod健康。反之异常。

Exec探测方式的yaml文件语法如下：

```yaml
spec:  
  containers:  
  - name: liveness  
    image: k8s.gcr.io/busybox  
    args:  
    - /bin/sh  
    - -c  
    - touch /tmp/healthy; sleep 30; rm -rf /tmp/healthy; sleep 600  
    livenessProbe:         #选择livenessProbe的探测机制  
      exec:                      #执行以下命令  
        command:  
        - cat  
        - /tmp/healthy  
      initialDelaySeconds: 5          #在容器运行五秒后开始探测  
      periodSeconds: 5               #每次探测的时间间隔为5秒  
```

在上面的配置文件中，探测机制为在容器运行5秒后，每隔五秒探测一次，如果cat命令返回的值为“0”，则表示健康，如果为非0，则表示异常。

2）Httpget： 通过发送http/htps请求检查服务是否正常，返回的状态码为200-399则表示容器健康（注http get类似于命令curl -I）。

Httpget探测方式的yaml文件语法如下：

```yaml
spec:  
  containers:  
  - name: liveness  
    image: k8s.gcr.io/liveness  
    livenessProbe:              #采用livenessProbe机制探测  
      httpGet:                  #采用httpget的方式  
    scheme:HTTP         #指定协议，也支持https  
        path: /healthz          #检测是否可以访问到网页根目录下的healthz网页文件  
        port: 8080              #监听端口是8080  
      initialDelaySeconds: 3     #容器运行3秒后开始探测  
      periodSeconds: 3                #探测频率为3秒  
```

上述配置文件中，探测方式为项容器发送HTTP GET请求，请求的是8080端口下的healthz文件，返回任何大于或等于200且小于400的状态码表示成功。任何其他代码表示异常。

3）tcpSocket： 通过容器的IP和Port执行TCP检查，如果能够建立TCP连接，则表明容器健康，这种方式与HTTPget的探测机制有些类似，tcpsocket健康检查适用于TCP业务。

tcpSocket探测方式的yaml文件语法如下：

```yaml
spec:  
  containers:  
  - name: goproxy  
    image: k8s.gcr.io/goproxy:0.1  
    ports:  
- containerPort: 8080  
#这里两种探测机制都用上了，都是为了和容器的8080端口建立TCP连接  
    readinessProbe:  
      tcpSocket:  
        port: 8080  
      initialDelaySeconds: 5  
      periodSeconds: 10  
    livenessProbe:  
      tcpSocket:  
        port: 8080  
      initialDelaySeconds: 15  
      periodSeconds: 20  
```

在上述的yaml配置文件中，两类探针都使用了，在容器启动5秒后，kubelet将发送第一个readinessProbe探针，这将连接容器的8080端口，如果探测成功，则该pod为健康，十秒后，kubelet将进行第二次连接。

除了readinessProbe探针外，在容器启动15秒后，kubelet将发送第一个livenessProbe探针，仍然尝试连接容器的8080端口，如果连接失败，则重启容器。

探针探测的结果有以下三种可能：

- Success：Container通过了检查；
- Failure：Container没有通过检查；
- Unknown：没有执行检查，因此不采取任何措施（通常是我们没有定义探针检测，默认为成功）。

## 19. 如何控制滚动更新过程

可以通过下面的命令查看到更新时可以控制的参数：

```shell
kubectl explain deploy.spec.strategy.rollingUpdate 
```

maxSurge：　此参数控制滚动更新过程，副本总数超过预期pod数量的上限。可以是百分比，也可以是具体的值。默认为1。

（上述参数的作用就是在更新过程中，值若为3，那么不管三七二一，先运行三个pod，用于替换旧的pod，以此类推）

maxUnavailable： 此参数控制滚动更新过程中，不可用的Pod的数量。

（这个值和上面的值没有任何关系，举个例子：我有十个pod，但是在更新的过程中，我允许这十个pod中最多有三个不可用，那么就将这个参数的值设置为3，在更新的过程中，只要不可用的pod数量小于或等于3，那么更新过程就不会停止）。

## 20. 镜像下载策略是什么

可通过命令“kubectl explain pod.spec.containers”来查看imagePullPolicy这行的解释。

K8s的镜像下载策略有三种：Always、Never、IFNotPresent；

- Always：镜像标签为latest时，总是从指定的仓库中获取镜像；
- Never：禁止从仓库中下载镜像，也就是说只能使用本地镜像；
- IfNotPresent：仅当本地没有对应镜像时，才从目标仓库中下载。
- 默认的镜像下载策略是：当镜像标签是latest时，默认策略是Always；当镜像标签是自定义时（也就是标签不是latest），那么默认策略是IfNotPresent。

## 21. image的状态有哪些

- Running：Pod所需的容器已经被成功调度到某个节点，且已经成功运行，
- Pending：APIserver创建了pod资源对象，并且已经存入etcd中，但它尚未被调度完成或者仍然处于仓库中下载镜像的过程
- Unknown：APIserver无法正常获取到pod对象的状态，通常是其无法与所在工作节点的kubelet通信所致。

## 22. pod的重启策略是什么？

可以通过命令“kubectl explain pod.spec”查看pod的重启策略。（restartPolicy字段）

- Always：但凡pod对象终止就重启，此为默认策略。
- OnFailure：仅在pod对象出现错误时才重启

## 23. K8s中部署应用版本回滚的命令

```shell
kubectl apply -f httpd2-deploy1.yaml --record  
#运行yaml文件，并记录版本信息； 

kubectl rollout history deployment httpd-devploy1  
#查看该deployment的历史版本 

kubectl rollout undo deployment httpd-devploy1 --to-revision=1   
#执行回滚操作，指定回滚到版本1 
```

## 24. 标签和标签选择器的作用是什么？

标签：是当相同类型的资源对象越来越多的时候，为了更好的管理，可以按照标签将其分为一个组，为的是提升资源对象的管理效率。

标签选择器：就是标签的查询过滤条件。目前API支持两种标签选择器：

- 基于等值关系的，如：“=”、“ ” “= =”  、  “！=”（注：“==”也是等于的意思，yaml文件中的matchLabels字段）；
- 基于集合的，如：in、notin、exists（yaml文件中的matchExpressions字段）；

## 25. 常用的标签分类有哪些？

标签分类是可以自定义的，但是为了能使他人可以达到一目了然的效果，一般会使用以下一些分类：

- 版本类标签（release）：stable（稳定版）、canary（金丝雀版本，可以将其称之为测试版中的测试版）、beta（测试版）；
- 环境类标签（environment）：dev（开发）、qa（测试）、production（生产）、op（运维）；
- 应用类（app）：ui、as、pc、sc；
- 架构类（tier）：frontend（前端）、backend（后端）、cache（缓存）；
- 分区标签（partition）：customerA（客户A）、customerB（客户B）；
- 品控级别（Track）：daily（每天）、weekly（每周）

## 26. 查看标签的方式

```shell
kubectl get pod --show-labels  #查看pod，并且显示标签内容 

kubectl get pod -L env,tier    #显示资源对象标签的值 

kubectl get pod -l env,tier    #只显示符合键值资源对象的pod，而“-L”是显示所有的pod 
```

## 27. 添加、修改觉删除标签的命令

```shell
#对pod标签的操作 
kubectl label pod label-pod abc=123   #给名为label-pod的pod添加标签 
kubectl label pod label-pod abc=456 --overwrite   #修改名为label-pod的标签 
kubectl label pod label-pod abc-       #删除名为label-pod的标签 
kubectl get pod --show-labels 

#对node节点的标签操作   
kubectl label nodes node01 disk=ssd   #给节点node01添加disk标签 
kubectl label nodes node01 disk=sss –overwrite  #修改节点node01的标签 
kubectl label nodes node01 disk-     #删除节点node01的disk标签 
```

## 28. DaemonSet资源对象的特性

DaemonSet这种资源对象会在每个k8s集群中的节点上运行，并且每个节点只能运行一个pod，这是它和deployment资源对象的最大也是唯一的区别。

## 29. Pod的生命周期有哪些状态？

- Pending：表示pod已经被同意创建，正在等待kube-scheduler选择合适的节点创建，或者正在准备镜像；
- Running：表示pod中所有的容器已经被创建，并且至少有一个容器正在运行或者是正在启动或者是正在重启；
- Succeeded：表示所有容器已经成功终止，并且不会再启动；
- Failed：表示pod中所有容器都是非0（不正常）状态退出；
- Unknown：表示无法读取Pod状态，通常是kube-controller-manager无法与Pod通信。

## 30. 创建一个Pod的流程是如何的？

![创建pod流程图](/images/k8s常见总结2.assets/创建pod流程图.png)

![1465170-20190403102521028-1937176408](/images/k8s常见总结2.assets/1465170-20190403102521028-1937176408.png)

- 客户端提交Pod的配置信息（可以是yaml文件定义好的信息）到kube-apiserver；
- Apiserver收到指令后，通知给controller-manager创建一个资源对象；
- Controller-manager通过api-server将pod的配置信息存储到ETCD数据中心中；
- Kube-scheduler检测到pod信息会开始调度预选，会先过滤掉不符合Pod资源配置要求的节点，然后开始调度调优，主要是挑选出更适合运行pod的节点，然后将pod的资源配置单发送到node节点上的kubelet组件上。
- Kubelet根据scheduler发来的资源配置单运行pod，运行成功后，将pod的运行信息返回给scheduler，scheduler将返回的pod运行状况的信息存储到etcd数据中心。

## 31. 删除一个Pod的流程是如何的？

Kube-apiserver会接受到用户的删除指令，默认有30秒时间等待优雅退出，超过30秒会被标记为死亡状态，此时Pod的状态Terminating，kubelet看到pod标记为Terminating就开始了关闭Pod的工作；

关闭流程如下：

- pod从service的endpoint列表中被移除；
- 如果该pod定义了一个停止前的钩子，其会在pod内部被调用，停止钩子一般定义了如何优雅的结束进程；
- 进程被发送TERM信号（kill -14）
- 当超过优雅退出的时间后，Pod中的所有进程都会被发送SIGKILL信号（kill -9）。

## 32. K8s的service是什么？

Pod每次重启或者重新部署，其IP地址都会产生变化，这使得pod间通信和pod与外部通信变得困难，这时候，就需要Service为pod提供一个固定的入口。

Service的Endpoint列表通常绑定了一组相同配置的pod，通过负载均衡的方式把外界请求分配到多个pod上。

## 33. k8s如何进服务注册？

Pod启动后会加载当前环境所有Service信息，以便不同Pod根据Service名进行通信。

## 34. K8s数据持久化的方式有哪些？

​`emptyDir:`emptyDir是最基础的Volume类型，用于存储临时数据的简单空目录。如果Pod设置了emptyDir类型Volume，Pod被分配到Node上时候，会创建emptyDir，只要Pod运行在Node上，emptyDir都会存在（容器挂掉不会导致emptyDir丢失数据），但是如果Pod从Node上被删除（Pod被删除，或者Pod发生迁移），emptyDir也会被删除，并且永久丢失。

​`Hostpath:`将宿主机上已存在的目录或文件挂载到容器内部。类似于docker中的bind mount挂载方式。这种数据持久化方式，运用场景不多，因为它增加了pod与节点之间的耦合。

​`PersistentVolume:` PersistentVolume(持久卷， 简称 PV)和Persistent VolumeClaim(持久卷声明，简称 PVC)使得K8s集群具备了存储的逻辑抽象能力，使得在配置Pod的逻辑里可以忽略对实际后台存储技术的配置，而把这项配置的工作交给PV的配置者，即集群的管理者。存储的PV和PVC的这种关系，跟计算的Node和Pod的关系是非常类似的；PV和Node是资源的提供者，根据集群的基础设施变化而变化，由K8s集群管理员配置；而PVC和Pod是资源的使用者，根据业务服务的需求变化而变化，由K8s集群的使用者即服务的管理员来配置。
