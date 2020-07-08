### spug 部署
```
部署容器
docker run -d --name=spug -p 80:80 registry.aliyuncs.com/openspug/spug
初始化账号密码
docker exec spug init_spug admin 123456
重启容器
docker restart spug
```

