## 7、1.监控系统
### prometheus 部署
#### 组件介绍
```
prometheus  //服务核心组件
alertmanager //配置告警阀值相关
blackbox_exporter //监控业务容器存活性
consul_exporter
graphite_exporter
haproxy_exporter
memcached_exporter
mysqld_exporter
node_exporter //主机性能及硬件指标采集
pushgateway
statsd_exporter
```
### 下载镜像
```
docker pull prom/node-exporter
docker pull prom/prometheus
docker pull grafana/grafana
```

### prometheus启动    
```
docker run  -d \
  -p 9090:9090 \
  -v /data/local/prometheus/prometheus.yml:/etc/prometheus/prometheus.yml  \
  prom/prometheus
```
prometheus.yml
```
global:
  scrape_interval:     10s   #获取刷新
  evaluation_interval: 10s   #规则刷新

scrape_configs:
  - job_name: prometheus       #自定义job
    static_configs:
      - targets: ['localhost:9090']     #监控客户端到插件地址
        labels:
          instance: prometheus       #自定义标签
```
### grafana 启动
```
docker run -d \
  -p 3000:3000 \
  --name=grafana \
  -v /data/local/grafana-storage:/var/lib/grafana \
  grafana/grafana
```

### linux主机监控
```
docker run -d -p 9100:9100 \
  -v "/proc:/host/proc:ro" \
  -v "/sys:/host/sys:ro" \
  -v "/:/rootfs:ro" \
  --net="host" \
  prom/node-exporter
```

### mysql监控
```
docker run -d \
  -p 9104:9104 \
  -e DATA_SOURCE_NAME="user:password@(my-mysql-network:3306)/" \
  prom/mysqld-exporter
```
  
### docker监控
```
docker run \
  --volume=/:/rootfs:ro \
  --volume=/var/run:/var/run:ro \
  --volume=/sys:/sys:ro \
  --volume=/var/lib/docker/:/var/lib/docker:ro \
  --volume=/dev/disk/:/dev/disk:ro \
  --publish=8090:8080 \
  --detach=true \
  --name=cadvisor \
  google/cadvisor
```
#### 添加docker 修改prometheus的 prometheus.yml文件
```
- job_name: docker
  static_configs:
    - targets: ['g.mooom.top:8090']
      labels:
        instance: docker
```

### pushgateway代理

```
docker run -d -p 9091:9091 prom/pushgateway
```

#### 添加pushgateway  修改prometheus的 prometheus.yml文件
```
- job_name: pushgateway    
  static_configs:
    - targets: ['g.mooom.top:9091']  
      labels:
        instance: pushgateway  
```
  

#### 向time的job中添加值time为3
```
echo "time 3" | curl --data-binary @- http://g.mooom.top:9091/metrics/job/time
```

#### 删除time的job
```
curl -X DELETE http://g.mooom.top:9091/metrics/job/time
```
