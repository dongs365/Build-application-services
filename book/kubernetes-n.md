## 9.2 kubernetes -内网
### 部署环境
|名称|系统|类别|
|:-:|:-:|:-:|
|master|centos|主节点|
|node1|centos|从节点|
|node2|centos|从节点|

### docker 安装
```
curl -fsSL https://get.docker.com | bash -s docker --mirror Aliyun
```
### kubectl 安装
```
cat <<EOF > /etc/yum.repos.d/kubernetes.repo
[kubernetes]
name=Kubernetes
baseurl=https://mirrors.aliyun.com/kubernetes/yum/repos/kubernetes-el7-x86_64/
enabled=1
gpgcheck=1
repo_gpgcheck=1
gpgkey=https://mirrors.aliyun.com/kubernetes/yum/doc/yum-key.gpg https://mirrors.aliyun.com/kubernetes/yum/doc/rpm-package-key.gpg
EOF
setenforce 0
yum install -y kubelet kubeadm kubectl
systemctl enable kubelet && systemctl start kubelet
```
### 修改docker模式
```
cat > /etc/docker/daemon.json <<EOF
{
  "exec-opts": ["native.cgroupdriver=systemd"],
  "log-driver": "json-file",
  "log-opts": {
    "max-size": "100m"
  },
  "storage-driver": "overlay2",
  "registry-mirrors": ["https://am3hfczx.mirror.aliyuncs.com"]
}
EOF
systemctl daemon-reload
systemctl restart docker
```

### 写入内核配置
```
echo vm.swappiness=0 >> /etc/sysctl.conf
echo 1 > /proc/sys/net/bridge/bridge-nf-call-iptables
```

### 关闭selinux & 关闭swap分区
```
setenforce 0
sed -i "s/^SELINUX=enforcing/SELINUX=disabled/g" /etc/selinux/config
swapoff -a
sed -i 's/.*swap.*/#&/' /etc/fstab
```

### kubernetes安装部署
#### pull 镜像 master节点和node节点操作
```
curl https://raw.githubusercontent.com/dongs365/docker-images/master/pull.sh|bash
```

#### 初始化 master主节点操作 (记录输出的kubeadm join xxxxxxxx)
```
kubeadm init --pod-network-cidr=10.244.0.0/16
```

#### 用户添加变量信息
```
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
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
kubectl get nodes  # 查看节点
kubectl get pods  # 查看pod 
```

#### 命令补全
```
yum install -y bash-completion
source /usr/share/bash-completion/bash_completion
source <(kubectl completion bash)
echo "source <(kubectl completion bash)" >> ~/.bashrc
```
