# Docker 的安装

## CentOS 环境初始化
可参考 [CentOS 7.5 环境初始化](../linux/centos-init.md)

## Dcoker 安装（root 用户）
```shell
yum install yum-utils device-mapper-persistent-data lvm2 bash-completion
# sudo yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo 这里我们使用 aliyun 镜像来加速，官方镜像网速太慢，甚至可能卡死
yum-config-manager --add-repo http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
yum list docker-ce.x86_64 --showduplicates | sort -r
export docker_version=18.06.1.ce-3.el7
yum install --setopt=obsoletes=0 docker-ce-${docker_version}  # 指定版本安装
sudo systemctl enable docker && sudo systemctl start docker
```

### 修该存储目录
```shell
vi /etc/docker/daemon.json
{
  "data-root":"/var/lib/docker"
}
```