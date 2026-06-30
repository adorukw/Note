# 云服务器部署nextcloud

## 一. 准备工作
1. 在nextcloud官网下载latest.zip，并通过scp命令上传到服务器，再解压到/var/www/目录下
2. 使用以下命令创建数据库：
    ```sql
    CREATE DATABASE nextcloud CHARACTER SET utf8mb4 COLLATE utf8mb4_general_ci;
    CREATE USER 'nextclouduser'@'localhost' IDENTIFIED BY '744226Nextcloud';
    GRANT ALL PRIVILEGES ON nextcloud.* TO 'nextclouduser'@'localhost';
    FLUSH PRIVILEGES;
    EXIT;
    ```
3. 使用以下命令安装所需依赖：
    ```bash
    # 更新系统
    sudo apt update && sudo apt upgrade -y

    # 安装必要组件
    sudo apt install -y nginx mariadb-server php-fpm \
    php-cli php-mysql php-zip php-xml php-mbstring \
    php-gd php-curl php-intl php-ldap php-imagick \
    php-bcmath php-gmp unzip wget curl
    ```

## 二. 配置apache
1. 使用以下命令编辑apache端口:
    ```
    sudo nano /etc/apache2/ports.conf
    ```
    添加以下内容：
    ```
    Listen 8081
    ```
2. 使用以下命令编辑apache配置文件：
    ```
    sudo nano /etc/apache2/sites-available/nextcloud.conf
    ```
    修改内容如下：
    ```
    <VirtualHost *:8081>
    ServerName localhost
    ServerAdmin webmaster@localhost
    DocumentRoot /var/www/nextcloud

    # 大文件上传支持
    LimitRequestBody 10737418240

    ErrorLog ${APACHE_LOG_DIR}/nextcloud_error.log
    CustomLog ${APACHE_LOG_DIR}/nextcloud_access.log combined

    <Directory /var/www/nextcloud>
        Options Indexes FollowSymLinks
        AllowOverride All
        Require all granted
    </Directory>
    </VirtualHost>
    ```
3. 使用以下命令启用配置文件：
    ```
    sudo a2ensite nextcloud.conf
    ```
4. 使用以下命令重启apache：
    ```
    sudo systemctl restart apache2
    ```

## 三. 配置nextcloud
1. 编辑配置文件：
    ```
    sudo nano /var/www/nextcloud/config/config.php
    ```
    修改内容如下：
    ```
    <?php
    $CONFIG = array (
    'instanceid' => 'ocqkd3sz1nik',
    'overwritewebroot' => '/nextcloud',
    'overwriteprotocol' => 'https',
    'overwritehost' => 'www.h01f0c017.nyat.app',
    'overwrite.cli.url' => 'https://www.h01f0c017.nyat.app/nextcloud',
    'trusted_domains' => 
    array (
        0 => 'www.h01f0c017.nyat.app',
    ),
    'trusted_proxies' => 
    array (
        0 => '127.0.0.1',
    ),
    'csp.allowed' => true,
    'passwordsalt' => 'ufcbvykdBMvQi8q4T38fCBEBPh/bz1',
    'secret' => '312/RAV/G792D90nO51KFb+jZslY8ECrH8qF+6xFd8EDcZIT',
    'datadirectory' => '/var/www/nextcloud/data',
    'dbtype' => 'mysql',
    'version' => '32.0.3.2',
    'dbname' => 'nextcloud',
    'dbhost' => 'localhost',
    'dbtableprefix' => 'oc_',
    'mysql.utf8mb4' => true,
    'dbuser' => 'nextclouduser',
    'dbpassword' => '744226Nextcloud',
    'installed' => true,
    );
    ```

## 四. 配置Nginx反向代理
1. 使用以下命令配置nginx：
    ```
    sudo nano /etc/nginx/sites-available/web
    ```
    添加内容如下：
    ```
    #  location / 块匹配所有未匹配其他规则的请求
    location /nextcloud/ {
        # 将接收到的所有请求转发到本地8081端口（即Apache）
        proxy_pass http://localhost:8081/;
        # 将原始请求的Host头部信息传递给后端服务器，这对WordPress正确识别域名至关重要
        proxy_set_header Host $http_host;
        # 以下两行是强烈建议添加的，用于传递客户端的真实IP和协议
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
        proxy_set_header X-Forwarded-Host $host;
    }
    ```

## 五. 启动nextcloud
1. 进入网址https://www.h01f0c017.nyat.app:26481/nextcloud/，输入用户名、密码、数据库配置等，完成安装
2. 安装完成后使用 https://www.h01f0c017.nyat.app:26481/nextcloud/index.php/apps/dashboard/ 访问控制面板，因为再通过 https://www.h01f0c017.nyat.app:26481/nextcloud/ 进入会被重定向到https://www.h01f0c017.nyat.app/nextcloud/index.php/apps/dashboard/ (这里少了:26481端口)