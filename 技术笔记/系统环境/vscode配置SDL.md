### 📌 在 VS Code + MinGW 中配置 SDL2（项目文件夹下）总结

#### 🔧 步骤概览：
1. **下载 SDL2 开发库**
2. **项目文件夹结构准备**
3. **VS Code 配置**
4. **编译 & 运行配置**
5. **测试代码**

---

#### 📂 1. 项目文件夹结构准备
```
你的项目文件夹/
├── .vscode/             # VS Code 配置目录
├── include/             # 存放头文件
│   └── SDL2/            # 从 SDL 解压包复制
│       ├── SDL.h
│       └── ...
├── lib/                 # 存放库文件
│   ├── libSDL2.a        # MinGW 静态库
│   └── libSDL2.dll.a    # MinGW 链接库
├── dll/                 # 存放运行时 DLL
│   └── SDL2.dll         # 必需动态链接库
└── main.cpp             # 你的源代码
```

> 📦 **所需文件来源**：
> - 从 https://www.libsdl.org/download-2.0.php 下载 **SDL2-devel-2.x.x-mingw.zip**（选 MinGW 版本）
> - 解压后复制：
>   - `include/SDL2/` → 你的项目 `/include/SDL2`
>   - `lib/x64/*.a` → 你的项目 `/lib`（根据系统选 32/64 位）
>   - `lib/x64/SDL2.dll` → 你的项目 `/dll`

---

#### ⚙️ 2. VS Code 配置（`.vscode` 目录下）

##### (1) `c_cpp_properties.json` - 智能提示
```json
{
    "configurations": [
        {
            "name": "MinGW",
            "intelliSenseMode": "gcc-x64",
            "compilerPath": "C:/MinGW/bin/g++.exe",  // 你的 MinGW 路径
            "includePath": [
                "${workspaceFolder}/include/**"      // 关键路径！
            ],
            "defines": [],
            "cStandard": "c17",
            "cppStandard": "gnu++17"
        }
    ],
    "version": 4
}
```

##### (2) `tasks.json` - 编译配置
```json
{
    "version": "2.0.0",
    "tasks": [
        {
            "label": "build-sdl2",
            "type": "shell",
            "command": "g++",
            "args": [
                "-g",
                "${workspaceFolder}/main.cpp",
                "-I${workspaceFolder}/include",         // 头文件路径
                "-L${workspaceFolder}/lib",            // 库文件路径
                "-o", "${workspaceFolder}/main.exe",
                "-lmingw32",                          // 必需顺序！
                "-lSDL2",
                "-lSDL2main",
                "-mwindows"                           // 隐藏控制台窗口
            ],
            "group": {
                "kind": "build",
                "isDefault": true
            },
            "problemMatcher": ["$gcc"]
        }
    ]
}
```

##### (3) `launch.json` - 调试配置
```json
{
    "version": "0.2.0",
    "configurations": [
        {
            "name": "(gdb) Launch",
            "type": "cppdbg",
            "request": "launch",
            "program": "${workspaceFolder}/main.exe",
            "args": [],
            "stopAtEntry": false,
            "cwd": "${workspaceFolder}",
            "environment": [
                {
                    "name": "PATH",
                    "value": "${workspaceFolder}/dll;%PATH%" // 添加 DLL 路径
                }
            ],
            "externalConsole": false,
            "MIMode": "gdb",
            "miDebuggerPath": "C:/MinGW/bin/gdb.exe",   // 你的 gdb 路径
            "preLaunchTask": "build-sdl2"                // 编译任务名
        }
    ]
}
```

---

#### 🚨 关键注意事项
1. **链接顺序必须为**：`-lmingw32` → `-lSDL2` → `-lSDL2main`
2. **DLL 位置**：
   - 运行时需确保 `SDL2.dll` 在系统 PATH 中
   - 配置中的 `environment` 已设置项目 `/dll` 优先加载
   - 或将 `SDL2.dll` 直接复制到 `.exe` 同级目录
3. **调试问题**：
   - 若窗口闪退 → 在代码末尾添加 `SDL_Delay(3000)`
   - 若链接失败 → 检查库文件路径和文件名大小写

---

#### ✅ 4. 测试代码 (`main.cpp`)
```cpp
#include <SDL.h>

int main(int argc, char* argv[]) {
    SDL_Init(SDL_INIT_VIDEO);
    SDL_Window* window = SDL_CreateWindow(
        "SDL2 成功运行!",
        SDL_WINDOWPOS_CENTERED, SDL_WINDOWPOS_CENTERED,
        800, 600, 
        SDL_WINDOW_SHOWN
    );
    
    SDL_Delay(3000);  // 显示 3 秒
    
    SDL_DestroyWindow(window);
    SDL_Quit();
    return 0;
}
```

---

#### 🚀 操作流程：
1. 按 `Ctrl+Shift+B` 编译
2. 按 `F5` 调试运行
3. 成功显示窗口 → 配置完成！

> 💡 提示：若要显示控制台输出（如 `printf`），删除 `tasks.json` 中的 `"-mwindows"` 参数。

通过此配置，你的项目可完全独立运行，无需修改系统路径！ 🔥