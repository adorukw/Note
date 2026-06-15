# debian配置git

## 一. 安装git
1. 使用以下命令`sudo apt update && sudo apt install git`安装git

## 二. 配置git
1. 使用以下命令`git config --global user.name "your name"`设置用户名
2. 使用以下命令`git config --global user.email "your email"`设置邮箱
3. 使用以下命令`git config --global credential.helper store`保存用户名和密码

## 三. 克隆远程仓库到本地
1. git clone https://github.com/用户名/仓库名.git .

## 四. 克隆远程仓库后的第一次推送流程
1. `git add .`
2. `git commit -m "第一次推送"`
3. `git push --set-upstream origin main`
4. `git branch --set-upstream-to=origin/main`

## 五.解决代理无法连接github
1. 使用以下命令
    ```
    git config --global http.proxy http://127.0.0.1:7897
    git config --global https.proxy https://127.0.0.1:7897
    ```
    其中，端口为代理端口