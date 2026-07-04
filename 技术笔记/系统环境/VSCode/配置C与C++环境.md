# Debian系统在VSCode中配置C与C++环境

## 一. 准备工作
1. 在VSCode官网中下载deb安装包并安装
2. 使用命令`sudo apt-get update`更新软件源
3. 使用命令`sudo apt-get install build-essential`安装编译环境
4. 在VSCode中安装C/C++扩展

## 二. 配置C++语言环境
1. 在VSCode中打开C项目文件夹，创建新的`main.c`文件
2. 按下`ctrl+shift+p`打开命令面板，输入`C/C++: Edit Configurations`将编译器路径设置为`/urs/bin/g++`
3. 按下`ctrl+shift+p`打开命令面板，输入`Task: Configure Task`，选择C/C++:g++活动生成文件
4. 在`tasks.json`文件中修改下面配置项
   ```json
    "group": {
        "kind": "build",
        "isDefault": true
    }
   ```
   用来将该任务设置为默认编译任务
5. 按下`ctrl+shift+d`打开调试面板，选择`创建新的调试配置json文件`，选择C++(GDB/LLDB)
6. 在`launch.json`文件中修改下面配置项
   ```json
    "program": "${fileDirname}/${fileBasenameNoExtension}",
    "externalConsole": true,
    "preLaunchTask": "C/C++: g++ 生成活动文件"
   ```
   其中`program`项指定运行的程序，`${fileDirname}/${fileBasenameNoExtension}`表示程序位于当前文件所在目录，`${fileBasenameNoExtension}`表示程序名为当前文件名（不含扩展名），`externalConsole`项表示运行程序时是否使用外部命令行窗口，`preLaunchTask`项表示运行程序前执行的编译任务（这里设置为刚才配置的编译任务的task.json中的label配置项）