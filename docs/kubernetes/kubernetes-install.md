# Kubernetes 的安装

## 环境需求
1. 系统: CentOS Linux release 7.5.1804 (Core)

## CentOS 7.5 系统环境的初始化
可参考 [CentOS 7.5 环境初始化](../linux/centos-init.md)

## Docker 的安装
可参考 [Docker 的安装](../docker/install-docker.md)

## Kubernets 的安装

### Aliyun Kubernetes Yum 源配置
```shell
cat <<EOF > /etc/yum.repos.d/kubernetes.repo
[kubernetes]
name=Kubernetes
baseurl=http://mirrors.aliyun.com/kubernetes/yum/repos/kubernetes-el7-x86_64
enabled=1
gpgcheck=0
repo_gpgcheck=0
gpgkey=http://mirrors.aliyun.com/kubernetes/yum/doc/yum-key.gpg
       http://mirrors.aliyun.com/kubernetes/yum/doc/rpm-package-key.gpg
EOF
```

### 开始安装
会自动安装 cri-tools kubernets-cni socat 依赖
```shell
yum install kubeadm kubelet kubectl
```

### Kubernetes Master
1. 拉取镜像: kubeadm config images pull  # 
2. 初始化 Master: kubeadm init --kubernetes-version=1.12.2 --pod-network-cidr=10.244.0.0/16
3. mkdir -p $HOME/.kube  
   sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config  
   sudo chown $(id -u):$(id -g) $HOME/.kube/config  

### 安装 Pod Network 插件
这里我们选择使用 Canal 来创建网络
```shell
kubectl apply -f https://docs.projectcalico.org/v3.3/getting-started/kubernetes/installation/hosted/canal/rbac.yaml
kubectl apply -f https://docs.projectcalico.org/v3.3/getting-started/kubernetes/installation/hosted/canal/canal.yaml
```

执行 `kubectl get pods --all-namespaces` 命令，等待所有的 STATUS 为 running 之后，表示正常，等待几分钟即可！
```
NAMESPACE     NAME                                                     READY   STATUS    RESTARTS   AGE
kube-system   canal-mccpl                                              3/3     Running   0          6m23s
kube-system   coredns-576cbf47c7-h9cfl                                 1/1     Running   4          13m
kube-system   coredns-576cbf47c7-q4fhm                                 1/1     Running   4          13m
kube-system   etcd-ecs-192-168-11-101.dripsea.com                      1/1     Running   1          13m
kube-system   kube-apiserver-ecs-192-168-11-101.dripsea.com            1/1     Running   2          13m
kube-system   kube-controller-manager-ecs-192-168-11-101.dripsea.com   1/1     Running   1          12m
kube-system   kube-proxy-pxhch                                         1/1     Running   1          13m
kube-system   kube-scheduler-ecs-192-168-11-101.dripsea.com            1/1     Running   1          13m
```

### 取消 Master 隔离（可选）
默认情况下，出于安全原因，您的集群不会调度 Master 上的 pods
```shell
kubectl taint nodes --all node-role.kubernetes.io/master-
```

## 问题排查

### kubeadm config images pull 拉取进项失败
如果拉取镜像失败，可以尝试如下方法:  
```
docker pull mirrorgooglecontainers/kube-apiserver-amd64:v1.12.2
docker pull mirrorgooglecontainers/kube-controller-manager-amd64:v1.12.2
docker pull mirrorgooglecontainers/kube-scheduler-amd64:v1.12.2
docker pull mirrorgooglecontainers/kube-proxy-amd64:v1.12.2
docker pull mirrorgooglecontainers/pause:3.1
docker pull mirrorgooglecontainers/etcd-amd64:3.2.24
docker pull coredns/coredns:1.2.2

docker tag docker.io/mirrorgooglecontainers/kube-apiserver-amd64:v1.12.2 k8s.gcr.io/kube-apiserver:v1.12.2
docker tag docker.io/mirrorgooglecontainers/kube-controller-manager-amd64:v1.12.2 k8s.gcr.io/kube-controller-manager:v1.12.2
docker tag docker.io/mirrorgooglecontainers/kube-scheduler-amd64:v1.12.2 k8s.gcr.io/kube-scheduler:v1.12.2
docker tag docker.io/mirrorgooglecontainers/kube-proxy-amd64:v1.12.2 k8s.gcr.io/kube-proxy:v1.12.2
docker tag docker.io/mirrorgooglecontainers/pause:3.1  k8s.gcr.io/pause:3.1
docker tag docker.io/mirrorgooglecontainers/etcd-amd64:3.2.24  k8s.gcr.io/etcd:3.2.24
docker tag docker.io/coredns/coredns:1.2.2  k8s.gcr.io/coredns:1.2.2

docker rmi mirrorgooglecontainers/kube-apiserver-amd64:v1.12.2
docker rmi mirrorgooglecontainers/kube-controller-manager-amd64:v1.12.2
docker rmi mirrorgooglecontainers/kube-scheduler-amd64:v1.12.2
docker rmi mirrorgooglecontainers/kube-proxy-amd64:v1.12.2
docker rmi mirrorgooglecontainers/pause:3.1
docker rmi mirrorgooglecontainers/etcd-amd64:3.2.24
docker rmi coredns/coredns:1.2.2
```

## 参考
https://kubernetes.io/docs/setup/independent/install-kubeadm/
https://kubernetes.io/docs/setup/independent/create-cluster-kubeadm/