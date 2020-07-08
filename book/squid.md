### squid部署 http代理
```
docker run -d -p 3128:3128 minimum2scp/squid
```
#### 修改配置文件
```
http_access allow all
```
#### 本地使用
```
export http_proxy=http://127.0.0.1:3128
curl http://example.com/
```
