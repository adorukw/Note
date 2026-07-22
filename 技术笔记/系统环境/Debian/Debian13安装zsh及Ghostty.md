# Debian 13 安装 Zsh 及配置

## 一、安装

### 1.1 安装 Zsh 与 Oh My Zsh

```bash
sudo apt update
sudo apt install zsh
sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
chsh -s $(which zsh)
```

### 1.2 检查 Oh My Zsh 安装

```bash
ls ~/.oh-my-zsh
```

### 1.3 安装 Ghostty

前往 GitHub 仓库下载 Ghostty 的 AppImage。

---

## 二、配置

### 2.1 安装 Powerlevel10k 主题

```bash
git clone --depth=1 https://github.com/romkatv/powerlevel10k.git \
    ${ZSH_CUSTOM:-$HOME/.oh-my-zsh/custom}/themes/powerlevel10k
```

编辑配置文件：

```bash
vim ~/.zshrc
```

找到以下行：

```bash
ZSH_THEME="robbyrussell"
```

替换为：

```bash
ZSH_THEME="powerlevel10k/powerlevel10k"
```

加载配置：

```bash
source ~/.zshrc
```

#### 字体安装

下载 MesloLGS NF 字体（包含 Regular、Bold、Italic、Bold Italic 四个文件）。

#### 更新主题

```bash
git -C ${ZSH_CUSTOM:-$HOME/.oh-my-zsh/custom}/themes/powerlevel10k pull
```

### 2.2 安装插件

#### 自动补全

```bash
git clone https://github.com/zsh-users/zsh-autosuggestions \
    ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-autosuggestions
```

#### 语法高亮

```bash
git clone https://github.com/zsh-users/zsh-syntax-highlighting.git \
    ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting
```

#### 补全增强

```bash
git clone https://github.com/zsh-users/zsh-completions \
    ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-completions
```

### 2.3 重新配置主题

```bash
p10k configure
```

---

## 三、Ghostty 配置

### 3.1 查看内置主题

```bash
ghostty +list-themes
```

### 3.2 编辑配置文件

在 Ghostty 配置文件中添加：

```
theme = GitHub Dark Colorblind
background-opacity = 0.85
background-blur-radius = 30
```

---

**排版调整说明：**
- 统一使用 `#` 标题层级，结构清晰
- 命令与说明文字分离，代码块标注 `bash`
- 使用 `---` 分隔大章节
- 同类操作（三个插件）使用同级小标题并列展示
- 保留原内容所有命令和配置，未做任何增删
