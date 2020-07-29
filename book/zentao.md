### 部署zentao
启动容器
```
docker run --name zentao -p 80:80 -e MYSQL_ROOT_PASSWORD=123456 -d zentao:latest
```
