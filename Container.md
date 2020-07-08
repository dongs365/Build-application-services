## 9、容器仓库
### docker仓库 harbor 部署
#### 准备域名，https证书，下载安装包

#### 下载安装包
```
wget https://storage.googleapis.com/harbor-releases/release-1.8.0/harbor-offline-installer-v1.8.2.tgz
```
#### 解压配置
```
tar zxvf harbor-offline-installer-v1.8.2.tgz
cd harbor
```
#### 修改配置
vim harbor.yml（修改访问地址，访问密码，是否https）
```
hostname: reg.mooom.top
http:
  port: 80
https:
  port: 443
  certificate: /data/ssl/reg.mooom.top.pem
  private_key: /data/ssl/reg.mooom.top.key
harbor_admin_password: 123456
```
#### 开始安装
``` 
./prepare
./install.sh
docker-compose ps
```
如果harbor出现问题，可以执行以下命令重启harbor
docker-compose stop       //停止运行容器
docker-compose rm         //删除容器
systemctl daemon-reload
service docker restart    //重启docker
docker-compose up -d      //启动harbor

#### 登陆仓库
```
docker login reg.mooom.top
```

#### 退出登陆
```
docker logout
```


### registry 仓库

创建密码
```
docker run --rm --entrypoint htpasswd registry -Bbn admin 123456 > auth/htpasswd
```
启动容器
```
docker run -d \
  --restart=always \
  --name registry \
  -v `pwd`/auth:/auth \
  -e "REGISTRY_AUTH=htpasswd" \
  -e "REGISTRY_AUTH_HTPASSWD_REALM=Registry Realm" \
  -e REGISTRY_AUTH_HTPASSWD_PATH=/auth/htpasswd \
  -v `pwd`/ssl:/certs \
  -e REGISTRY_HTTP_ADDR=0.0.0.0:443 \
  -e REGISTRY_HTTP_TLS_CERTIFICATE=/certs/g.mooom.top.pem \
  -e REGISTRY_HTTP_TLS_KEY=/certs/g.mooom.top.key \
  -p 443:443 \
  -p 5000:5000 \
  registry
```




