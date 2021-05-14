## Homework
本次完成了Basic和 Advanced的作业。
### Basic
1. 在本地安装 kubectl 命令行以及任意 k8s 运行环境（MiniKube / Docker Desktop / 其他）
2. 简述 kubectl logs / describe / apply / delete 命令的功能
- logs: Print the logs for a container in a pod or specified resource. If the pod has only one container, the container name is optional.
- describe: Print a detailed description of the selected resources, including related resources such as events or controllers. 
- apply: Apply a configuration to a resource by filename or stdin. The resource name must be specified. This resource will be created if it doesn't exist yet. 
- delete: delete resources by filenames, stdin, resources and names, or by resources and label selector.
3. 将示例中的 Node.js 应用（或自定义其他工程）通过 Deployment 部署，经过Service组织后由Ingress暴露出可被访问的API（使用kubectl apply）
- deploy app resource.`kubectl apply -f k8s-deploy.yaml`
- deploy ingress controller.`kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v0.46.0/deploy/static/provider/cloud/deploy.yaml`
- deploy ingress resource.`kubectl apply -f k8s-ingress.yaml`
4. 使用 kubectl get查看本地运行的所有pod，deployment，service，使用kubectl describe查看pod，deployment的详细信息
5. 使用 kubectl logs 查看正在运行的pod的日志
6. 使用 kubectl port-forward 命令将本地请求直接转发到 pod

### Advanced
1. 安装 k8s dashboard，通过 dashboard 进行扩容 / 收缩；使用kubectl scale命令对deployment进行扩容 / 收缩
- k8s dashboard
    - kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v2.2.0/aio/deploy/recommended.yaml
    - kubectl proxy
    - kubectl -n kubernetes-dashboard get secret $(kubectl -n kubernetes-dashboard get sa/admin-user -o jsonpath="{.secrets[0].name}") -o go-template="{{.data.token | base64decode}}"
    - http://localhost:8001/api/v1/namespaces/kubernetes-dashboard/services/https:kubernetes-dashboard:/proxy/
    [选择deployment-scale](images/dashboard1.jpg)-[设置新的scale](images/dashboard2.jpg)
- kubectl scale
    - kubectl scale deployment/myapp --replicas 2
2. 简单描述 Pod, Node, Deployment, Service, Ingress, ReplicaSet, Namespace 概念
- pod: k8s中的最小部署单元，不是一个程序/进程，而是一个环境(包括容器、存储、网络ip:port、容器配置)。其中可以运行1个或多个container（docker或其他容器），在一个pod内部的container共享所有资源，包括共享pod的ip:port和磁盘。pod中的容器会作为一个整体被master调度到一个node上运行。
- node: node的职责是运行容器应用。node由master管理，node负责监控并汇报容器的状态，同时根据master的要求管理容器的生命周期。node运行在linux的操作系统上，可以是物理机或者是虚拟机。
- services: 由于pod是临时性的，pod的ip:port也是动态变化的。这种动态变化在k8s集群中就涉及到一个问题：如果一组后端pod作为服务提供方，供一组前端的pod所调用，那服务调用方怎么自动感知服务提供方。这就引入了k8s中的另外一个核心概念，services.
service是通过apiserver创建出来的对象实例。
- [ingress](images/ingress.jpg): 通常情况下，Service 和 Pod 的 IP 仅可在集群内部访问。Ingress 可以给 Service 提供集群外部访问的 URL、负载均衡、SSL 终止、HTTP 路由等。
- replicaset: 实现了pod的多副本管理。使用deployment时会自动创建replicaset，也就是说deployment是通过replicaset来管理pod的多个副本的，我们通常不需要直接使用replicaset。
- namespace: 可以将一个物理的cluster逻辑上划分成多个虚拟cluster，每个cluster就是一个namespace。不同的namespace里的资源是完全隔离的。 
3. 创建一个kubernetes [cronjob](k8s-yamls/k8s-cronjob.yaml)（扩展 Node.js 应用或使用其他工程）

