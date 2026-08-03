# 漫画图片文件夹合并转 epub 流程经验总结

> 任务：把 95 个章节文件夹（每话若干张 jpg/jpeg 图片）合并成一个带目录的单本 epub
> 日期：2026-08-02 ｜ 环境：Arch Linux ｜ 工具：pandoc 3.6.1

---

## 一、核心结论

- **pandoc 不能把 jpg 直接转 epub**，它只能转换文本（Markdown/HTML 等）。
- 正确姿势：**先生成一份引用图片路径的 Markdown**，再让 pandoc 把图片嵌入 epub。
- Markdown 里的 `#` 标题会自动生成 epub 目录（TOC）。

## 二、完整流程

### 1. 摸清目录结构

```bash
# 统计图片类型与数量
find . -type f | grep -oP '\.\K[a-zA-Z0-9]+$' | sort | uniq -c

# 查看文件夹层级（注意嵌套：本任务有「其他特别篇」文件夹，里面还有子文件夹）
find . -maxdepth 2 -type d | sort
```

本任务结构：95 个顶层章节文件夹 + `其他特别篇/` 下的 9 个子文件夹，共 **2574 张图**（753 jpeg + 1821 jpg），另有 `ComicInfo.xml` 需忽略。

### 2. 生成 Markdown（用 Python 脚本）

要点：
- 章节文件夹按**数字自然排序**（第22话 后接 第22.1话，而不是按字符串 22.1 排在 2 前）。
- 每章用 `## 标题`，图片用 `![](路径)`。
- **图片引用必须写成 `![](<路径>)` 尖括号形式**（见"坑"第 3 条）。

核心脚本逻辑：

```python
import os, re

def natural_key(name):
    return [int(t) if t.isdigit() else t.lower() for t in re.split(r"(\d+)", name)]

def list_images(folder):
    items = [f for f in os.listdir(folder)
             if os.path.splitext(f)[1].lower() in (".jpg", ".jpeg")]
    items.sort(key=natural_key)   # 1.jpg, 2.jpg ... 10.jpg 顺序正确
    return items

def main_chapter_key(name):       # 正章在前，按话数排
    m = re.search(r"第(\d+(?:\.\d+)?)话", name)
    return (0, float(m.group(1))) if m else (1, natural_key(name))
```

### 3. 转换（关键参数）

```bash
pandoc 全部.md -o 漫画.epub \
  --toc --toc-depth=2 \
  --split-level=2 \
  --metadata title="漫画名" --metadata lang="zh-CN" \
  --css=style.css
```

- `--toc` 生成目录，`--toc-depth=2` 目录包含 h1+h2。
- `--split-level=2` 按 h2 标题拆分章节文件（**必须加**，否则 100 章会堆进一个 xhtml，600MB 单文件阅读器会卡死）。
- `--css=style.css` 自定义样式（漫画图满宽显示）。

样式表（图片满宽、居中）：

```css
p { margin: 0; text-align: center; }
img { max-width: 100%; height: auto; }
h1, h2 { text-align: center; page-break-before: always; }
```

### 4. 验证结果

```bash
unzip -l 漫画.epub | grep -c 'EPUB/media/'      # 应等于图片总数
unzip -p 漫画.epub EPUB/nav.xhtml               # 查看目录是否完整
unzip -l 漫画.epub | grep -c 'EPUB/text/.*xhtml' # 每章一个文件
```

---

## 三、踩过的坑（重点）

### 1. 概念误区：pandoc "不能转 jpg"
pandoc 的输入是**排版稿**（Markdown），jpg 只是被 `![]()` 引用的资源，由 pandoc 负责打包进 epub。想转图片必须先有引用它们的 Markdown。

### 2. pandoc 3.6 移除了 `--epub-stylesheet`
直接报 `--epub-stylesheet has been removed. Use --css instead.`。老教程里的写法在 3.x 已失效。

### 3. 文件夹名含**两个空格**时图片找不到
`YSJの舅舅  13特别篇` 里有两个空格。裸 URL 写法 `![](路径)` 会被 Markdown 解析器把**连续空格折叠成一个**，pandoc 编码成 `%20` 后找不到文件。
**解决**：用尖括号 `![](<路径>)` 包裹，pandoc 会保留原路径并正确编码为 `%20%20`。

### 4. 全章挤进一个文件
默认 epub 只按 h1 拆分，若不指定 `--split-level=2`，所有 h2 章节会合成一个巨大的 xhtml。

### 5. 安装 pandoc（无 sudo 权限时的备选方案）
本机 sudo 需密码，无法 `pacman -S pandoc`。备选：下载官方静态二进制放到 `~/.local/bin`：

```bash
curl -sL https://github.com/jgm/pandoc/releases/download/3.10.1/pandoc-3.10.1-linux-amd64.tar.gz -o /tmp/p.tar.gz
tar -xzf /tmp/p.tar.gz -C /tmp/p --strip-components=1
cp /tmp/p/bin/pandoc ~/.local/bin/
```

### 6. 其他小坑
- Python 环境对单字母变量名 `l` 解析异常（本机特定现象），建议避免用 `l` 做循环变量。
- 图片命名不统一（`1.jpg` vs `001.jpeg`），排序必须用自然排序而非字典序。
- `--metadata lang="zh-CN"` 会提示找不到 zh 翻译文件，仅警告，不影响产物。

---

## 四、效果与遗留

- **产出**：`YSJの舅舅.epub`，605MB，101 章目录完整，2574 张图全部嵌入，每章独立文件。
- **遗留文件**：`全部.md`（源 Markdown）、`style.css`（样式），改图后可直接重新 pandoc。
- **体积问题**：原图高清，epub 偏大；如需减小可对图片降采样（如缩到 1600px 宽）再打包。
