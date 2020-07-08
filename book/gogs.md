### gogs 部署
#### 部署mysql
```
docker run --name mysql5.7 -p 3306:3306 -e MYSQL_ROOT_PASSWORD=123456 -d mysql:5.7
```
#### 创建数据库
```
CREATE DATABASE `gogs` CHARACTER SET utf8 COLLATE utf8_general_ci;
```
#### 部署gogs
```
docker run -d --name=mygogs -p 10022:22 -p 3000:3000 -v /var/gogs:/data gogs/gogs
```

登陆配置 IP:3000
#### gogs 非root用户启用 1024 以下端口
##### 添加附加权限
setcap 'cap_net_bind_service=+ep' /home/gogs/gogs/gogs
##### 查看附加权限
getcap gogs
##### 清除附加权限
setcap -r

