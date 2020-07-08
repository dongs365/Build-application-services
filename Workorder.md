## 3、工单系统
### 部署jira
```
docker run -d -p 8080:8080 --name jira cptactionhank/atlassian-jira-software:latest
```

### 部署zentao
```
docker run --name zentao -p 80:80 -e MYSQL_ROOT_PASSWORD=123456 -d zentao:latest
```
