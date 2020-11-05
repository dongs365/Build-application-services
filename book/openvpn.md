#### openvpn 一键安装脚本
```
https://github.com/Nyr/openvpn-install
wget https://git.io/vpn -O openvpn-install.sh
```
##### 内核开启转发
```
net.ipv4.ip_forward = 1
```
#### 密码验证文件 checkpsw.sh
```
#!/bin/sh
###########################################################
# checkpsw.sh (C) 2004 Mathias Sundman <mathias@openvpn.se>
#
# This script will authenticate OpenVPN users against
# a plain text file. The passfile should simply contain
# one row per user with the username first followed by
# one or more space(s) or tab(s) and then the password.

PASSFILE="/etc/openvpn/psw-file"
LOG_FILE="/var/log/openvpn-password.log"
TIME_STAMP=`date "+%Y-%m-%d %T"`

###########################################################

if [ ! -r "${PASSFILE}" ]; then
  echo "${TIME_STAMP}: Could not open password file \"${PASSFILE}\" for reading." >> ${LOG_FILE}
  exit 1
fi

CORRECT_PASSWORD=`awk '!/^;/&&!/^#/&&$1=="'${username}'"{print $2;exit}' ${PASSFILE}`

if [ "${CORRECT_PASSWORD}" = "" ]; then
  echo "${TIME_STAMP}: User does not exist: username=\"${username}\", password=\"${password}\"." >> ${LOG_FILE}
  exit 1
fi

if [ "${password}" = "${CORRECT_PASSWORD}" ]; then
  echo "${TIME_STAMP}: Successful authentication: username=\"${username}\"." >> ${LOG_FILE}
  exit 0
fi

echo "${TIME_STAMP}: Incorrect password: username=\"${username}\", password=\"${password}\"." >> ${LOG_FILE}
exit 1
```
#### 账号密码文件 psw-file
```
#user  password
abc 123456
```

##### 服务器配置文件
```
local 10.10.10.1
port 1194
proto tcp
dev tun
#使用脚本验证密码
#auth-user-pass-verify /etc/openvpn/checkpsw.sh via-env
ca ca.crt
cert server.crt
key server.key
dh dh.pem
auth SHA512
tls-crypt tc.key
topology subnet
server 10.8.0.0 255.255.255.0
#保存已有的用户和ip的对应关系
ifconfig-pool-persist ipp.txt
# 所有流量通过vpn，一般注释
#push "redirect-gateway def1 bypass-dhcp" 
# 自定义dns
push "dhcp-option DNS 202.96.128.166"
# 自定义路由
push "route 10.10.10.0 255.255.255.0 vpn_gateway"
#不要求客户端有证书
#client-cert-not-required
#username-as-common-name
#如果密码验证客户端需要加入 auth-user-pass
keepalive 10 120
cipher AES-256-CBC
user nobody
group nobody
persist-key
persist-tun
status openvpn-status.log
log /var/log/openvpn.log
verb 3
```
#### 客户端配置
##### 安装服务
```
yum install openvpn 
apt install openvpn
```
##### 启动服务
```
nohup openvpn --config ./test1.ovpn &
```
##### supervisor启动服务
```
[program:dsvpn]
directory = /etc/openvpn/client
command =  openvpn --config /etc/openvpn/client/test1.ovpn
autostart = true
startsecs = 5
autorestart = true
startretries = 3
user = root
redirect_stderr = true
stdout_logfile_maxbytes = 20MB
stdout_logfile_backups = 20
stdout_logfile = /var/log/supervisor/dsvpn.log
```
#### 容器化
##### 启动容器
```
docker run -d \
 --name=openvpn-as \
 --cap-add=NET_ADMIN \  
 -e PUID=1000 \
 -e PGID=1000 \
 -e TZ=Europe/London \
 -e INTERFACE=eth0 `#optional`\
 -p 943:943 \
 -p 9443:9443 \
 -p 1194:1194/udp \
 --restart unless-stopped \
 ghcr.io/linuxserver/openvpn-as

```
默认账号密码admin/password
