# Docker 的安装

## CentOS 环境初始化
可参考 [CentOS 7.5 环境初始化](../linux/centos-init.md)

## 创建 docker 用户，并且赋予各种权限
```shell
useradd docker  # 创建 docker 用户
passwd docker  # 设置 docker 用户的密码
echo "docker  ALL=(ALL)       NOPASSWD: ALL" >> /etc/sudoers  # 为 docker 添加 sudo 免密码权限
```

## Dcoker 安装（docker 用户）
```shell
su - docker
sudo yum install yum-utils device-mapper-persistent-data lvm2 bash-completion
# sudo yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo 这里我们使用 aliyun 镜像来加速，官方镜像网速太慢，甚至可能卡死
sudo yum-config-manager --add-repo http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
export docker_version=18.06.1.ce-3.el7
sudo yum install --setopt=obsoletes=0 docker-ce-${docker_version}  # 指定版本安装
# sudo usermod -aG docker `other_user`  # 如果是非 docker 用户（也不是 root），那么得把该用户添加到 docker 组中去，可以参考上面的 `创建 docker 用户，并且赋予各种权限`
# 默认情况下，请注意 /etc/docker /var/lib/docker /var/log/docker 的权限
sudo systemctl enable docker && systemctl start docker
```