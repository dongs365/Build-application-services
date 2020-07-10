## 9.2 kubernetes 文档
### 部署环境
|名称|系统|类别|
|:-:|:-:|:-:|
|master|centos|主节点|
|node1|centos|从节点|
|node2|centos|从节点|

### docker 安装
```
curl -fsSL https://get.docker.com -o get-docker.sh
sh get-docker.sh
```
### kubectl 安装
```
cat <<EOF > /etc/yum.repos.d/kubernetes.repo
[kubernetes]
name=Kubernetes
baseurl=https://packages.cloud.google.com/yum/repos/kubernetes-el7-x86_64
enabled=1
gpgcheck=1
repo_gpgcheck=1
gpgkey=https://packages.cloud.google.com/yum/doc/yum-key.gpg https://packages.cloud.google.com/yum/doc/rpm-package-key.gpg
EOF
yum install -y kubelet kubeadm kubectl
systemctl enable kubelet.service
```
### 修改docker模式
```
cat > /etc/docker/daemon.json <<EOF
{
  "exec-opts": ["native.cgroupdriver=systemd"]
}
EOF
```
### 关闭swap分区
```
#注释swap  /etc/fstab
#/dev/mapper/centos-swap swap         swap   defaults   0 0
```
### 写入内核配置
```
echo vm.swappiness=0 >> /etc/sysctl.conf
```
### 关闭selinux
```
vim /etc/selinux/config
SELINUX=enforcing改为SELINUX=disabled
```

```
setenforce 0
sed -i "s/^SELINUX=enforcing/SELINUX=disabled/g" /etc/selinux/config
swapoff -a
sed -i 's/.*swap.*/#&/' /etc/fstab
```
### kubernetes安装部署
#### 初始化 master主节点操作 (记录输出的kubeadm join xxxxxxxx)
```
kubeadm init --pod-network-cidr=10.244.0.0/16
```
#### 安装网络 master主节点操作
```
kubectl apply -f https://docs.projectcalico.org/manifests/calico.yaml
```
#### 从节点加入集群 node从节点操作
```
kubeadm join xxxxxxxxxxxxxxxxxxxxxxx 
```
#### 常用命令
```
kubectl get node  # 查看节点
kubectl get node  # 查看pod
```
