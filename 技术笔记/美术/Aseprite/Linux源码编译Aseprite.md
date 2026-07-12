# Linux源码编译Aseprite

## 一. 安装依赖
```
sudo apt update
sudo apt install -y g++ cmake ninja-build libx11-dev libxcursor-dev libxi-dev libgl1-mesa-dev libfontconfig1-dev git wget
```

## 二. 准备编译用文件夹
1. `~/AAAPAN/Project/Temp/aseprite-build `

## 三. 下载官方预编译的 Skia
1. 访问 [官方编译的skia的Latest](https://github.com/aseprite/skia/releases/latest)
2. 找到[Skia-Linux-Release-x64.zip](https://github.com/aseprite/skia/releases/download/m124-08a5439a6b/Skia-Linux-Release-x64.zip)下载并解压到`aseprite-build/deps/skia`

## 四. 获取Aseprite源码
1. 进入``~/AAAPAN/Project/Temp/aseprite-build`文件夹`git clone --recursive https://github.com/aseprite/aseprite.git`

## 五. 配置 CMake 生成构建文件
1. 进入 Aseprite 源码目录并创建 `build` 文件夹
```
mkdir build
cd build
```
2. 运行下列命令
```
cmake \
  -DCMAKE_BUILD_TYPE=RelWithDebInfo \
  -DLAF_BACKEND=skia \
  -DSKIA_DIR=/home/adorukw/AAAPAN/Project/Temp/aseprite-build/deps/skia \
  -DSKIA_LIBRARY_DIR=/home/adorukw/AAAPAN/Project/Temp/aseprite-build/deps/skia/out/Release-x64 \
  -DSKIA_LIBRARY=/home/adorukw/AAAPAN/Project/Temp/aseprite-build/deps/skia/out/Release-x64/libskia.a \
  -G Ninja \
  ..
```

## 六. 使用Ninjia开始编译
1. 在`build`文件夹下运行`ninja aseprite`

## 七. 创建桌面图标
1. 移动文件
```
mkdir /home/adorukw/AAAPAN/Sw/Aseprite

cp -r /home/adorukw/AAAPAN/Project/Temp/aseprite-build/aseprite/build/bin/* /home/adorukw/AAAPAN/Sw/Aseprite

cp /home/adorukw/AAAPAN/Project/Temp/aseprite-build/aseprite/src/desktop/linux/aseprite.desktop /home/adorukw/.local/share/applications

cp /home/adorukw/AAAPAN/Project/Temp/aseprite-build/aseprite/src/desktop/linux/mime/aseprite.xml /home/adorukw/.local/share/mime/packages
```
2. 更新图标和文件库
```
sudo update-desktop-database
sudo update-mime-database /home/adorukw/.local/share/mime/packages
```

## 八. 汉化
1. 进入仓库[aseprite-chinese](https://github.com/sc3531486/aseprite-chinese)
2. 下载[aseprite-chinese.aseprite-extension](https://github.com/sc3531486/aseprite-chinese/releases/download/v2.0.0/aseprite-chinese.aseprite-extension)
3. 打开 Aseprite
4. 点击菜单：**Edit > Preferences > Extensions > Add Extension**
5. 选择下载的 `.aseprite-extension` 文件
6. 重启 Aseprite
