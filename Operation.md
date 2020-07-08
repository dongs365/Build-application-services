## 11、运维系统
### spug 部署
```
部署容器
docker run -d --name=spug -p 80:80 registry.aliyuncs.com/openspug/spug
初始化账号密码
docker exec spug init_spug admin 123456
重启容器
docker restart spug
```

### jumpserver 堡垒机
```
git clone https://github.com/jumpserver/Dockerfile.git
cd Dockerfile
cat .env
docker-compose up
```

### docker 管理工具
```
docker run -d -p 9100:9000 \
    --restart=always \
    -v /var/run/docker.sock:/var/run/docker.sock \
    --name prtainer-local \
    portainer/portainer
```
