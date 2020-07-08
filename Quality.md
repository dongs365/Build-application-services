## 10、质量系统

### SonarQube部署
## postgresql 数据库
```
docker pull postgres
docker run --name postgres -p 5432:5432 --restart=always -v /data/local/postgresql:/var/lib/postgresql -e POSTGRES_USER=sonar -e POSTGRES_PASSWORD=sonar -d postgres
```
/var/lib/postgresql 数据库目录
postgresqldb  为容器名称  
POSTGRES_USER POSTGRES_PASSWORD 指定postgresql的用户名密码

### 中文
```
https://github.com/SonarQubeCommunity/sonar-l10n-zh/releases
ADD sonar-l10n-zh-plugin-1.19.jar /opt/sonarqube/extensions/plugins/
```
### c++支持插件
```
https://github.com/SonarOpenCommunity/sonar-cxx/releases
ADD sonar-cxx-plugin-1.3.1.1807.jar /opt/sonarqube/extensions/plugins/
```


### sonarqube
```
docker pull sonarqube
docker run --name sonarqube --link postgres:db --restart=always \
-e SONARQUBE_JDBC_URL=jdbc:postgresql://db:5432/sonar -p 9000:9000 -d sonarqube
```
--link postgresqldb 是指和 postgresqldb 容器连接通讯， 用网关的方式
#### 系统内核修改
```
sysctl -w vm.max_map_count=262144
```
