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
#### 第三方命令行监控
```
curl -Lo kubebox https://github.com/astefanutti/kubebox/releases/download/v0.8.0/kubebox-linux && chmod +x kubebox
```

#### 第三方web kuboard
```
# 安装
kubectl apply -f https://kuboard.cn/install-script/kuboard.yaml
kubectl apply -f https://addons.kuboard.cn/metrics-server/0.3.6/metrics-server.yaml
kubectl get pods -l k8s.kuboard.cn/name=kuboard -n kube-system
# Master 节点上执行此命令，获取token
echo $(kubectl -n kube-system get secret $(kubectl -n kube-system get secret | grep kuboard-user | awk '{print $1}') -o go-template='{{.data.token}}' | base64 -d)
# 访问
http://任意一个Worker节点的IP地址:32567/
```
#### dashboard部署
##### 下载dashboard yaml文件
```
wget https://raw.githubusercontent.com/kubernetes/dashboard/v2.0.0-beta8/aio/deploy/recommended.yaml
```
#### 修改yaml文件，添加 type: NodePort
```
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
  selector:
    k8s-app: kubernetes-dashboard
  type: NodePort
```
#### 应用dashboard文件
```
kubectl apply -f recommended.yaml
```

##### 创建用户文件 user.yaml
```
apiVersion: v1
kind: ServiceAccount
metadata:
  name: admin-user
  namespace: kubernetes-dashboard
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: admin-user
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
- kind: ServiceAccount
  name: admin-user
  namespace: kubernetes-dashboard
```
##### 应用用户文件
```
kubectl apply -f user.yaml
```

##### 查看token
```
kubectl -n kubernetes-dashboard describe secret $(kubectl -n kubernetes-dashboard get secret | grep admin-user | awk '{print $1}')
```

##### 查看NodePort
```
kubectl get svc -n kubernetes-dashboard
```

##### 访问 kubernetes-dashboard
```
https://ip:NodePort
```

#### 常用命令
```
kubectl get nodes  # 查看节点
kubectl get pods  # 查看pod 
```
#### nginx:部署/弹缩/删除/创建映射/删除映射
```
kubectl create deployment nginx --image=nginx
kubectl scale deployment nginx --replicas=10
kubectl delete deployments.apps nginx
kubectl expose deployment nginx --port=80 --type=NodePort
kubectl delete services nginx
```
#### 命令补全
```
yum install -y bash-completion
source /usr/share/bash-completion/bash_completion
source <(kubectl completion bash)
echo "source <(kubectl completion bash)" >> ~/.bashrc
```
