# 一句话先分两大块：

1. **apt 下载的安装包 (.deb 压缩包)**：临时存在 `/var/cache/apt/archives`，装完还会留在这占空间；
2. **apt 安装解压后散落的程序文件（二进制 / 库 / 头文件 / 文档）**：统一丢 `/usr` 下各类子目录，遵循 Linux 标准目录规范 FHS；
3. **软件系统配置**：丢 `/etc`；**程序运行产生的数据 / 日志**：丢 `/var`；**你自己用户的软件偏好**：`~/.config`。

## 一、先讲：apt 下载时，安装包（deb 压缩包）存在哪？

plaintext

```
/var/cache/apt/archives/
```

- 你执行 `apt install sdl2-dev gcc`，先从软件源下载 `.deb` 压缩包放到这里；
- 没下完的临时文件：`/var/cache/apt/archives/partial/`
- 安装完成后 deb 包不会自动删除，长期积累几十 G；
- 清理缓存命令：
    
    bash
    
    ```
    apt clean    # 清空所有缓存deb包
    apt autoclean # 只清理旧版本deb
    ```
    

> 这只是**安装压缩包缓存**，不是软件运行文件，删掉不影响已装好的程序。

## 二、安装解压后，各类文件分别 “拉屎” 到哪里（你写 C/SDL 最常用）

### 1. 可执行命令（能直接终端敲的程序：gcc、sdl2-config、weston、wayland 相关工具）

`/usr/bin/` 普通用户命令

`/usr/sbin/` root 管理员系统工具

例：`/usr/bin/gcc`、`/usr/bin/sdl2-config`

### 2. 开发头文件（你写 C 代码 #include 要用，安装 xxx-dev 包才会有）

`/usr/include/`

SDL 例子：`/usr/include/SDL2/SDL.h`，对应 `-I/usr/include/SDL2`

### 3. 动态 / 静态库文件（编译链接 -lSDL2 依赖的库）

64 位 Debian 标准路径：

`/usr/lib/x86_64-linux-gnu/`

静态库 `.a`、动态库 `.so` 全在这里；

sdl2-config --libs 底层就是读取这个目录。

### 4. 文档、man 手册、图标、资源、模板（架构无关只读文件）

`/usr/share/`

- man 手册：`/usr/share/man/man3/`（C 库函数手册）
- 软件文档：`/usr/share/doc/包名/`
- 窗口图标、配色、shader、示例代码都在这

### 5. 系统全局配置文件（服务、系统软件公共设置）

`/etc/`

例：`/etc/apt/sources.list`（软件源配置）、窗口系统、网络服务配置。

`apt remove` 默认保留 /etc 下配置；`apt purge` 连带配置一起删干净。

### 6. 软件运行时动态产生的数据、日志、数据库

`/var/`

- 日志：`/var/log/`
- 软件缓存、数据库：`/var/lib/`
    
    apt 软件源元数据缓存：`/var/lib/apt/lists/`

### 7. 当前用户私有配置（只对你这个用户生效，apt 不自动生成，程序运行才创建）

`~/.config/软件名/`

比如 VSCode、桌面软件、SDL 图形工具的用户自定义参数，卸载系统包不会删除这里。

## 三、快速命令：查看某个包全部安装路径（万能）

想知道 `libsdl2-dev` 所有文件散落在哪些文件夹，执行：

bash

```
dpkg -l libsdl2-dev # 确认已安装
dpkg -L libsdl2-dev # 列出该包所有文件完整路径
```

## 四、容易混淆的对比

1. `/usr`：apt 包管理器安装的系统软件（gcc、SDL、wayland、cmake 全在这）
2. `/usr/local`：**源码手动编译 make install** 的软件，apt 不会碰这里，二者完全隔离
3. `.vscode`：只是编辑器配置，**不属于 apt 安装的系统文件**，和 apt 目录体系无关

## 五、极简总结记忆

1. 下载的安装压缩包：`/var/cache/apt/archives`
2. 程序本体（命令 / 库 / 头文件 / 文档）：全部分散在 `/usr` 子目录
3. 系统全局配置：`/etc`
4. 运行日志 / 动态数据：`/var`
5. 用户个人软件设置：家目录 `~/.config`
6. 想看某个包全部文件：`dpkg -L 包名`