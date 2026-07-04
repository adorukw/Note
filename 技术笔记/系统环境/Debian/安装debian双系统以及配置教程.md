# 安装Debian双系统以及配置教程

## 一. 准备工作
1. 在debian官网下载网络安装版iso文件，并用refus烧录到u盘中
2. 用系统盘进行系统安装，对于N卡，图形安装界面可能出现问题，所以使用命令行安装即可

## 二. 安装Nvidia显卡驱动
1. 由于笔记本是N卡独显没有核显，所以直接近系统会卡在黑屏界面，只有左上角一个不动的光标
2. 解决方法是：在grub界面（即debian启动界面）的debian/liunux选项上按下e进入配置界面，在配置界面中找到开头为linux的那一行配置项，在其末尾加上nomodeset（让系统忽略显卡驱动），按下F10进入系统
3. 进入系统后在nvidia驱动下载官网找到显卡对应的驱动版本，为.run后缀文件
4. 禁用自带的nouveau驱动
    使用以下命令：
    ```
    sudo nano /etc/modprobe.d/blacklist.conf
    ```
    并在文件末尾添加
    ```
    blacklist nouveau
    options nouveau modeset=0
    ```
    最后执行：
    ```
    sudo update-initramfs -u
    ```
5. 使用
    ```bash 
    sudo chmod +x *.run
    ```
    给执行权限，使用
    ```bash
    sudo apt update && apt install build-essential
    ```
    安装gcc环境，使用
    ```bash
    sudo ./*.run
    ```
    执行安装程序
    注意，这里只能选择安装MIT/GPL开源驱动，否则不能使用

## 三. 卸载不用软件
1. 使用以下命令卸载ibus输入法
    ```
    sudo apt purge ibus
    ```
2. 使用以下命令卸载残留的配置文件
    ```bash
    sudo apt purge $(dpkg -l | grep ^rc | awk '{print $2}')
    ```
## 四. 设置快捷键打开终端
1. 打开设置-键盘，自定义快捷键，命令`gnome-terminal`，快捷键`ctrl+alt+t`

## 五. 设置外置显示器亮度调节
1. 使用命令`sudo apt install ddcutil` 安装
2. 使用以下命令`sudo gpasswd --add $USER i2c`将当前用户加入i2c组
3. 使用以下命令`sudo modprobe i2c-dev`加载i2c-dev模块

## 六. 安装开发环境
### python
1. 到anaconda官网下载anaconda安装脚本并执行
2. 创建Anaconda.desktop，写入：
    ```
    [Desktop Entry]
    Type=Application
    Name=Anaconda
    Exec=/home/adorukw/anaconda3/bin/anaconda-navigator
    Icon=/home/adorukw/anaconda3/lib/python3.13/site-packages/anaconda_navigator/static/images/common/anaconda-icon-256x256.png
    Terminal=false
    Categories=Development;
    ```
3. 使用以下命令`sudo mv ./Anaconda.desktop /usr/share/applications/`移动到应用桌面
### nodejs
1. 使用命令`curl -fsSL https://deb.nodesource.com/setup_lts.x | sudo -E bash -`更新源
2. 使用命令`sudo apt install nodejs`安装
3. 使用命令`npm config set registry https://registry.npmmirror.com/`更新npm源
4. 使用命令`npm config set registry https://registry.npmjs.org/ `还原npm源

## 七. 细节设置
1. 使用命令`sudo nano /etc/systemd/logind.conf`打开文件，找到下列选项并修改：
    ```
    HandleLidSwitch=ignore
    HandleLidSwitchExternalPower=ignore
    LidSwitchIgnoreInhibited=no
    ```
    即可让笔记本在盒盖时不会休眠，可让笔记本24小时运行

2. 使用xbindkeys配置鼠标侧键
   1. 使用以下命令`sudo apt install xbindkeys xdotool`安装xbindkeys和xdotool
   2. 使用以下命令`xbindkeys --defaults > ~/.xbindkeysrc`生成配置文件
   3. 使用以下命令`xev | grep button`监听鼠标侧键键号
   4. 在配置文件末尾添加：
        ```
        # 侧键8 -> 空格（跳跃）
        "xdotool key space"
        b:8

        # 侧键9 -> E（互动）
        "xdotool key e"
        b:9
        ```
    5. 使用以下命令重启xbindkeys：
        ```bash
        killall xbindkeys
        xbindkeys -f .xbindkeysrc
        ```
3. Desktop Icons插件提示缺少GnomeDesktop-3.0 这个 GIR 文件，使用以下命令安装：
    ```bash
    sudo apt install libgnome-desktop-3-dev
    ```

