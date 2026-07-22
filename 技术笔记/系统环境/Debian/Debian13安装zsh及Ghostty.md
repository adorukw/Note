# Debian13安装zsh及配置

## 一. 安装

1. 使用以下命令安装：
   ```zsh
   sudo apt update
   sudo apt install zsh
   sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
   chsh -s $(which zsh)
   ```
2. 检查安装`oh my zsh`情况：`ls ~/.oh-my-zsh`
3. 去github仓库安装ghostty的appimage

## 二. 配置

1. `zsh`安装`Powerlevel10k`主题
   ```zsh
   git clone --depth=1 https://github.com/romkatv/powerlevel10k.git \
      ${ZSH_CUSTOM:-$HOME/.oh-my-zsh/custom}/themes/powerlevel10k
  
   vim ~/.zshrc
   # 找到这一行
   ZSH_THEME="robbyrussell"
   
   # 替换为
   ZSH_THEME="powerlevel10k/powerlevel10k"
   source ~/.zshrc
   下载 MesloLGS NF 字体（Regular、Bold、Italic、Bold Italic 四个文件）
   更新主题 git -C ${ZSH_CUSTOM:-$HOME/.oh-my-zsh/custom}/themes/powerlevel10k pull

   # 自动补全
   git clone https://github.com/zsh-users/zsh-autosuggestions \
     ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-autosuggestions
   
   # 语法高亮
   git clone https://github.com/zsh-users/zsh-syntax-highlighting.git \
     ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting
   
   # 补全增强
   git clone https://github.com/zsh-users/zsh-completions \
     ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-completions
   ```
2. 修改ghostty配置  
   查看所有内置主题：`ghostty +list-themes`  
   打开配置文件加入
   ```
   theme = GitHub Dark Colorblind
   background-opacity = 0.85
   background-blur-radius = 30
   ```
