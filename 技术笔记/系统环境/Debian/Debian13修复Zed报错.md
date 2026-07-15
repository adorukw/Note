# Debian13修复Zed报错
## 一. Zed右上角报错Failed to Update
1. Zed 在 Linux 平台下的自动更新功能依赖 `rsync` 这个命令行工具。系统检测到你当前的环境中没有安装它，因此自动更新流程失败，并在右上角弹出了警告。
2. 使用`sudo apt install rsync`安装