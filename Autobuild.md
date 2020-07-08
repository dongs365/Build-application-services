## 5、构建系统
### 部署jenkins
```
docker run \
  -u root \
  -d \
  -p 8082:8080 \
  -p 50000:50000 \
  --restart=always \
  -v /data/local/jenkins:/var/jenkins_home \
  -v /var/run/docker.sock:/var/run/docker.sock \
  jenkinsci/blueocean
```
### 部署从节点 （先在主节点web启动方式配置建立从节点，查看信息）
```
docker run \
    -d  \
    -p 8084:8080 \
    -p 50004:50000 \
    -p 224:22 \
    --restart=always \
    -e JAVA_OPTS=-Duser.timezone=Asia/Shanghai \
    -v /data/local/jenkins4:/var/jenkins_home \
    -v /var/run/docker.sock:/var/run/docker.sock \
    --name jnlp-slave4   \
    jenkinsci/jnlp-slave:latest \
    -url https://jenkins.mooom.top 1qa2ws3ed4rf5tg6yh7uj8ik9ol0p1qa2ws3ed4rf5tg6yh7uj8ik9ol0p slave4
```

```
docker run \
    -d  \
    -p 8083:8080 \
    -p 50003:50000 \
    -p 223:22 \
    --restart=always \
    -e JAVA_OPTS=-Duser.timezone=Asia/Shanghai \
    -v /data/local/jenkins3:/var/jenkins_home \
    -v /var/run/docker.sock:/var/run/docker.sock \
    --name jnlp-slave3   \
    jenkinsci/jnlp-slave:latest \
    -url https://jenkins.mooom.top 1qa2ws3ed4rf5tg6yh7uj8ik9ol0p1qa2ws3ed4rf5tg6yh7uj8ik9ol0p slave3
```
