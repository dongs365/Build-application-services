### 部署ldap管理工具lam
```
docker run -p 8081:80 -it -d ldapaccountmanager/lam:stable
```
### 部署ldap管理工具phpldapadmin
```
docker run -d --privileged -p 8082:80  --env PHPLDAPADMIN_HTTPS=false --env PHPLDAPADMIN_LDAP_HOSTS=g.mooom.top -d osixia/phpldapadmin
```
### 部署ltb-self-service-password
#### 下载php配置文件
```
wget https://raw.githubusercontent.com/grams/docker-LTB-self-service-password/master/assets/config.inc.php
```
#### 修改php配置文件
```
# LDAP
$ldap_url = "ldap://g.mooom.top";
$ldap_starttls = false;
$ldap_binddn = "cn=admin,dc=mooom,dc=top";
$ldap_bindpw = "123456";
$ldap_base = "dc=mooom,dc=top";
$ldap_login_attribute = "uid";
$ldap_fullname_attribute = "cn";
$ldap_filter = "(&(objectClass=person)($ldap_login_attribute={login}))";

$use_questions = false;

## Mail
# LDAP mail attribute
$mail_attribute = "mail";
# Who the email should come from
$mail_from = "monitor@gmail.com";
$mail_from_name = "Self Service Password";
# Notify users anytime their password is changed
$notify_on_change = true;
# PHPMailer configuration (see https://github.com/PHPMailer/PHPMailer)
$mail_sendmailpath = '/usr/sbin/sendmail';
$mail_protocol = 'smtp';
$mail_smtp_debug = 0;
$mail_debug_format = 'html';
$mail_smtp_host = 'smtp.gmail.com';
$mail_smtp_auth = true;
$mail_smtp_user = 'monitor@gmail.com';
$mail_smtp_pass = '123456';
$mail_smtp_port = 465;
$mail_smtp_timeout = 30;
$mail_smtp_keepalive = false;
$mail_smtp_secure = 'ssl';
$mail_contenttype = 'text/plain';
$mail_charset = 'utf-8';
$mail_priority = 3;
$mail_newline = PHP_EOL;

$use_sms = false;
```
#### 启动容器
```
docker run -p 8083:80 -d -v ~/config.inc.php:/usr/share/self-service-password/conf/config.inc.php grams/ltb-self-service-password
```








