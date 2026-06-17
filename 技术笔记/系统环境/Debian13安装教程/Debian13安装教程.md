# Debian13安装教程

## 一. 下载Debian13ISO并烧录
1. 进入网站`https://www.debian.org/distrib/`![alt text](image.png)
点击`64 位 PC 网络安装 iso`下载得到![alt text](image-1.png)
2. 使用refus烧录到U盘![alt text](image-2.png)
---

## 二. 通过U盘镜像安装Debian13
---

## 三. 配置系统
1. 添加`adorukw`到sudoers中
    ```
    su
    sudo usermod -aG sudo adorukw
    newgrp sudo
    ```
2. 下载Edge并安装，卸载火狐
    ```
    sudo apt purge firefox-esr
    sudo apt autoremove
    ```
3. 卸载各种软件，并整理软件文件夹
4. 添加闭源下载源
    ```bash
    sudo vim /etc/apt/sources.list
    ```
    添加`non-free`和`non-free-firmware`
5. 安装MT7922网卡驱动`sudo apt install firmware-misc-nonfree network-manager`
6. 调整tweak中的选项
7. 修改截图、打开终端、返回桌面快捷键
8. 安装vscode
9. 安装扩展

## 四. 安装常用软件
1. vim
2. fastfetch
    ```bash
    sudo apt install fastfetch
    fastfetch --gen-config
    vim ~/.config/fastfetch/config.jsonc
    ```
3. QQ
4. 微信
5. QQ音乐（启动命令修改为`Exec=/opt/qqmusic/qqmusic %U --no-sandbox`）
6. WPS
7. 腾讯会议
8. Zotero
9.  Trae
10. Godot
11. Xournal++
12. Calibre
---

## 五. 安装各种扩展
1. 解决`GnomeDesktop-3.0 GIR file not found`
    ```bash
    sudo apt update
    sudo apt install gir1.2-gnomedesktop-3.0
    ```
2. 安装文件夹扩展依赖
    ```bash
    sudo apt install python3-nautilus
    ```

## 五. 配置开发环境
1. git
2. crul
    ```bash
    curl -O https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh
    chmod +x Miniconda3-latest-Linux-x86_64.sh
    bash Miniconda3-latest-Linux-x86_64.sh
    source ~/.bashrc
    ```
3. nvm
    ```bash
    curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.40.3/install.sh | bash
    echo 'export NVM_NODEJS_ORG_MIRROR=https://npmmirror.com/mirrors/node' >> ~/.bashrc
    source ~/.bashrc
    nvm install --lts
    npm config set registry https://registry.npmmirror.com/
    ```
4. gcc、gdb、cmake
    ```bash
    sudo apt install build-essential cmake gdb
    ```

5. Nginx
    安装Nginx
    ```bash
    sudo apt install nginx
    cd /etc/nginx/sites-available
    sudo touch web
    sudo ln -s /etc/nginx/sites-available/web /etc/nginx/sites-enabled/
    sudo rm /etc/nginx/sites-enabled/default
    sudo nginx -t
    sudo systemctl restart nginx
    sudo systemctl enable nginx
    sudo systemctl start nginx
    sudo systemctl status nginx
    ```
    编辑web配置文件
    ```bash
    sudo vim /etc/nginx/sites-available/web
    ```
    ```nginx
    server {
        listen 80;
        server_name localhost;
        
        location /blog/ {
            alias /home/adorukw/project/AdoruWorld/client/dist/;
            try_files $uri $uri/ /blog/index.html;
            # 静态资源缓存优化（可选推荐加）
            expires 1d;
            add_header Cache-Control "public, no-transform";
        }

        # 后端接口代理 /blog/api/
        location /blog/api/ {
            rewrite ^/blog(/.*)$ $1 break;
            proxy_pass http://127.0.0.1:8000;

            # 透传客户端真实信息
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header X-Forwarded-Proto $scheme;

            # 超时设置，防止接口长时间请求断开
            proxy_connect_timeout 60s;
            proxy_read_timeout 60s;
        }

        location /uploads/ { 
            rewrite ^/blog(/.*)$ $1 break;
            proxy_pass http://127.0.0.1:8000;
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header X-Forwarded-Proto $scheme; 
        }
    }
    ```
