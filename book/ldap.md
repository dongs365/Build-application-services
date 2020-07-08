### 部署openldap
#### 参数说明
```
--name 容器名称
--network 网络类型
--hostname  主机名称
--env LDAP_ORGANISATION LDAP组织者
--env LDAP_DOMAIN LDAP域
--env LDAP_ADMIN_PASSWORD LDAP密码,默认用户admin
```
#### 启动容器
```
docker run -d -p 389:389 --name myopenldap --network bridge --hostname openldap-host --env LDAP_ORGANISATION="mooom" --env LDAP_DOMAIN="mooom.top" --env LDAP_ADMIN_PASSWORD="123456" osixia/openldap
```









