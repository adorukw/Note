# Debian13安装zsh及配置

## 一. 安装
1. 使用以下命令安装：
    ```
    sudo apt update
    sudo apt install zsh
    sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
    chsh -s $(which zsh)
    ```
2. 检查安装`oh my zsh`情况：`ls ~/.oh-my-zsh`

## 二. 配置
1. 修改主题
    ```
    nano ~/.zshrc
    ZSH_THEME="robbyrussell"
    source ~/.zshrc
    ```
