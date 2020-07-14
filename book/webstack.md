#### webstack
#### 克隆代码：
```
git clone https://github.com/hui-ho/WebStack-Laravel.git
```
#### 安装依赖：
```
composer install
```
#### 复制配置：
```
cp .env.example .env
```
#### 编辑配置：
```
DB_DATABASE=database
DB_USERNAME=username
DB_PASSWORD=password
```
#### 生成 KEY：
```
php artisan key:generate  
```
#### 迁移数据：
```
php artisan migrate:refresh --seed
```
#### 本地测试：
```
php artisan serve
```
#### supervisord 配置进程
```
[program:webstack]
directory = /data/wwwroot/webstack/public
command = /opt/remi/php71/root/usr/bin/php -S 0.0.0.0:8081 /data/wwwroot/webstack/server.php
environment =
user = root
stopsignal = INT
autostart = true
autorestart = true
startsecs = 1
stderr_logfile = /var/log/supervisor/webstack.err.log
stdout_logfile = /var/log/supervisor/webstack.out.log
```
