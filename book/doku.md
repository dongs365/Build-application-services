### dokuwiki部署
#### 参数说明
```
DOKUWIKI_USERNAME: 管理员账号 Default: superuser
DOKUWIKI_FULL_NAME: 管理员名称 Default: Full Name
DOKUWIKI_PASSWORD: 管理员密码 Default: bitnami1
DOKUWIKI_EMAIL: 管理员邮箱 Default: user@example.com
DOKUWIKI_WIKI_NAME: 系统名称 Default: Bitnami DokuWiki
```
#### 启动容器
```
docker run -d -p 8003:80  -e DOKUWIKI_PASSWORD=123456 -v ～/dokuwiki:/bitnami/dokuwiki bitnami/dokuwiki:latest
```
