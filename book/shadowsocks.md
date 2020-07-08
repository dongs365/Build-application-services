### shadowsocks
```
docker run \
    -d \
    -p 8388:8388 \
    --restart=always \
    oddrationale/docker-shadowsocks \
    -s 0.0.0.0 \
    -p 8388 \
    -k mx*hu9PD \
    -m aes-256-cfb
```

