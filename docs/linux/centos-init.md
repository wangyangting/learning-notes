# CentOS 7.5 环境初始化

## 升级至 CeontOS 7.5
```shell
yum update
cat /etc/redhat-release 
CentOS Linux release 7.5.1804 (Core)  # 出现该信息之后，表示升级成功
```

## 主机名设置
FQDN 标准，例如: ecs-192-168-11-101.dripsea.com

## hosts 文件设置
```shell
vi /etc/hosts
# 192.168.11.101 ecs-192-168-11-101.dripsea.com
```

## 关闭 Selinx
```shell
sudo sed -i 's/SELINUX=enforcing/SELINUX=disabled/g' /etc/selinux/config
```

## 关闭防火墙（或者自行控制）
```
systemctl stop firewalld.service && systemctl disable firewalld.service
```

## 配置主机时间、时区、系统语言
```shell
# 查看时区
date -R 或者 timedatectl

# 修改时区
ln -sf /usr/share/zoneinfo/Asia/Shanghai /etc/localtime

# 修改系统语言环境
sudo echo 'LANG="en_US.UTF-8"' >> /etc/profile;source /etc/profile
```

## Kernel 内核性能调优
```shell
cat >> /etc/sysctl.conf << EOF
net.ipv4.ip_forward=1
net.ipv4.neigh.default.gc_thresh1=4096
net.ipv4.neigh.default.gc_thresh2=6144
net.ipv4.neigh.default.gc_thresh3=8192
EOF

sysctl -p
```