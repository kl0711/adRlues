## 安装 php

如果软件源没有php7.4 只有最新版8.0+ 用以下命令解决
```
sudo apt install software-properties-common
sudo add-apt-repository ppa:ondrej/php
```

```
sudo apt-get install php7.4 php7.4 php7.4-fpm php7.4-mbstring php7.4-xml php7.4-mysql php7.4-common php7.4-gd php7.4-json php7.4-cli php7.4-curl
```

一些扩展

```
apt install libapache2-mod-php \
php7.4-imagick php7.4-common php7.4-curl \
php7.4-gd php7.4-imap php7.4-intl \
php7.4-json php7.4-mbstring php7.4-mysql \
php7.4-ssh2 php7.4-xml php7.4-zip \
php7.4-apcu php7.4-redis redis-server
```

 
## 安装 apache2

```
apt install apache2
```

开启 apache 一些模块

```
sudo a2enmod ssl
sudo a2enmod proxy
sudo a2enmod proxy_http
sudo a2enmod rewrite
sudo a2enmod headers
```

### apache 配置

http 和 https 配置，强制转成 https
```
<VirtualHost *:80>
    ServerName test.com
    ServerAlias test.com
    DocumentRoot "/var/www/html"
    
    # 强制转成https
    <Directory "/var/www/html">
        # 新增
        RewriteEngine on
        RewriteCond %{SERVER_PORT} !^443$
        RewriteRule ^(.*)?$ https://%{SERVER_NAME}%{REQUEST_URI} [R=301,L]
    </Directory>
    
    # 如果不需要 https，设置下面这个就行了
    #<Directory "/var/www/html">
    #    Options  Indexes FollowSymLinks
    #    AllowOverride All
    #    Require all granted
    #</Directory>
</VirtualHost>

<VirtualHost 0.0.0.0:443>
 DocumentRoot "/var/www/html"
 #填写证书名称
 ServerName test.com
 ServerAlias test.com

 ProtocolsHonorOrder On
 Protocols h2 h2c http/1.1

 #启用 SSL 功能
 SSLEngine on
 #证书文件的路径
 SSLCertificateFile /etc/apache2/ssl/test.com.crt
 #私钥文件的路径
 SSLCertificateKeyFile /etc/apache2/ssl/test.com.key
 #证书链文件的路径
 SSLCertificateChainFile /etc/apache2/ssl/root_bundle.crt
 
 SetEnv HOME /var/www/html
 SetEnv HTTP_HOME /var/www/html

  <Directory /var/www/html>
       Options -Indexes
       AllowOverride All
       Order allow,deny
       allow from all
       AcceptPathInfo On
  </Directory>

</VirtualHost>
```

反向代理配置：

```
<Location />
        ProxyPass http://localhost:8160/
        ProxyPassReverse http://localhost:8160/
</Location>
```

## 安装 mysql

```
sudo apt install mysql-server mysql-client
```

设置 mysql 密码

```
sudo mysql_secure_installation
sudo mysql
ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'very_strong_password';
FLUSH PRIVILEGES;
```



新建用户、数据库，赋予权限

```
添加数据库
create database db_name;
CREATE USER 'db_username'@'localhost' IDENTIFIED BY 'very_strong_password';
GRANT ALL ON  db_name.* to 'db_username'@'localhost';
FLUSH PRIVILEGES;
exit;
```