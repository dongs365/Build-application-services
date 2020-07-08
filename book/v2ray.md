####  v2ray 一键安装脚本
```
bash <(curl -s -L https://git.io/v2ray.sh)
```

####  v2ray 容器部署
```
docker run \
    -d \
    --name v2ray \
    --restart always \
    -p 1747:1747 \
    -v /etc/localtime:/etc/localtime \
    v2ray/official
```
#### 配置文件
```
vim ~/v2ray/config.json
```

