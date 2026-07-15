# Zed配置快捷键快速打开md文件

## 一. 配置工作区的task.json

1. 在工作区根目录使用

```
mkdir .zed
touch .zed/tasks.json
```

2. 编辑.zed/tasks.json

```
[
  {
    "label": "View in Browser",
    "command": "xdg-open \"$ZED_FILE\";exit",
  },
]
```

## 二. 配置快捷键

1. 在Zed中`Ctrl+Shift+P`打开命令面板，输入`open keymap file`
2. 编辑`keymap.json`文件，添加以下内容：

```json
[
  {
    "context": "Workspace",
    "bindings": {
      "alt-b": "task::Spawn"
    }
  },
  {
    "context": "Editor",
    "bindings": {
      "ctrl-shift-b": ["task::Spawn", { "task_name": "View in Browser" }]
    }
  }
]
```
