### 1. 系统初始
centos 7
```
yum -y install epel-release
yum update -y
yum groupinstall 'Development Tools' -y 
yum -y install htop redhat-lsb vim lrzsz rsync git wget supervisor openvpn nload
```

### 2. docker部署
2.1 docker 最新版本安装
```
curl -fsSL https://get.docker.com | bash -s docker --mirror Aliyun
```
2.2 添加阿里云docker加速仓库
```
mkdir -p /etc/docker
tee /etc/docker/daemon.json <<-'EOF'
{
  "registry-mirrors": ["https://am3hfczx.mirror.aliyuncs.com"]
}
EOF
sudo systemctl daemon-reload
sudo systemctl restart docker
```

2.3 docker-composer 安装
```
curl -L https://github.com/docker/compose/releases/download/1.25.0/docker-compose-`uname -s`-`uname -m` -o /usr/bin/docker-compose
chmod +x /usr/bin/docker-compose
```

2.4 docker 命令tab补全
```
yum -y install bash-completion
source /usr/share/bash-completion/bash_completion
```

### 3. 系统配置
3.1关闭selinux
查看当前配置
```
enforceing 代表开启
permissive 代表警告
disabled 代表关闭
```
命令1
```
getenforce
```
命令2
```
/usr/sbin/sestatus -v 
```

3.1.1 selinux临时开启/关闭
设置SELinux 成为permissive模式,开启
```
setenforce 1 
```
设置SELinux 成为enforcing模式，关闭
```
setenforce 0
```

3.1.2 selinux永久关闭
```
vi /etc/selinux/config
将SELINUX=enforcing改为SELINUX=disabled
```
设置后需要重启才能生效


### 4. 防火墙配置
4.1 关闭防火墙
```
systemctl stop firewalld
systemctl disable firewalld
```

4.2 防火墙基本配置
开放端口段/端口
```
firewall-cmd --permanent --zone=public --add-port=2300-2400/tcp
firewall-cmd --permanent --zone=public --add-port=2300/udp
firewall-cmd --reload
firewall-cmd --list-all
```
