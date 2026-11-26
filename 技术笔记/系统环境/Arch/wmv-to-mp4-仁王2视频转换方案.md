# WMV → MP4 批量转换方案总结

> 转换对象:《仁王2》(Nioh 2)游戏目录 `movie/` 下的 529 个 .wmv 文件
> 转换日期:2026-08-06

## 一、背景与问题

源文件为游戏过场动画(开场/关卡剧情)与技能演示视频,共 **529 个 .wmv** 文件,原始总大小约 **4.3 GB**。

经 `ffprobe` 探测确认:

- 视频编码:**WMV3 (VC-1)**,分辨率 640×360 或 1920×1080,帧率 29.97 fps
- 音频:绝大多数文件**无音轨**
- 容器:ASF (.wmv)

### 为什么不能直接改后缀

MP4 容器**不支持 VC-1/WMV3 编码**,仅靠改后缀或 `-c copy` 流复制无法生成可播放的 MP4,必须用 H.264 等 MP4 兼容编码重新编码。

## 二、转换方案

### 命令

```bash
mkdir -p ToMp4 && find . -maxdepth 1 -name '*.wmv' -print0 | xargs -0 -P 8 -I {} bash -c 'f="$1"; ffmpeg -y -loglevel error -i "$f" -c:v libx264 -preset veryfast -crf 20 -c:a aac -b:a 192k "ToMp4/${f%.wmv}.mp4" || echo "FAILED: $f" >> ToMp4/convert_failures.log' _ {}
```

### 参数说明

| 参数 | 含义 |
|------|------|
| `-c:v libx264` | 视频转码为 H.264(MP4 标准兼容编码) |
| `-preset veryfast` | 编码速度优先,适合大批量转换 |
| `-crf 20` | 恒定质量 20(0-51,越小质量越高;20 ≈ 视觉无损) |
| `-c:a aac -b:a 192k` | 音频转 AAC 192kbps(无音轨文件自动跳过) |
| `-loglevel error` | 只输出错误信息,避免刷屏 |
| `xargs -P 8` | 8 路并行,充分利用多核 CPU |
| 输出目录 `ToMp4/` | 与原文件隔离,原文件保留不动 |

### 单文件转换(简单版)

```bash
ffmpeg -i 输入.wmv -c:v libx264 -preset veryfast -crf 20 -c:a aac -b:a 192k 输出.mp4
```

## 三、转换结果

| 指标 | 数值 |
|------|------|
| 源文件数 | 529 个 .wmv |
| 转换成功 | 529 个(失败 0) |
| 输出目录 | `ToMp4/` |
| 输出总体积 | 2.2 GB(约为原来的 51%,体积减半) |
| 视频编码 | H.264 (h264) |
| 分辨率 | 640×360 / 1920×1080,与原文件一致 |
| 时长 | 与原文件一致(抽查比对通过) |

### 验证方法

```bash
# 统计输出数量,应与源文件一致
ls ToMp4/*.mp4 | wc -l

# 抽查编码、分辨率、时长
ffprobe -v error -select_streams v:0 \
  -show_entries stream=codec_name,width,height \
  -show_entries format=duration \
  -of default=noprint_wrappers=1 ToMp4/EV0000.mp4
```

## 四、注意事项

1. **保留原文件**:转换不会修改或删除原 .wmv,如需删除请自行确认后再操作。
2. **游戏启动校验**:若该目录用于游戏内播放,删除/替换原文件可能导致游戏校验失败或无法读取,建议先备份或仅在确认无碍后清理。
3. **编码速度**:本机 24 核,8 路并行下全程约数分钟完成;文件多时可调大 `-P` 数值(如 `-P 16`)进一步加速。
4. **质量调节**:需要更小体积可提高 CRF(如 `-crf 23`);追求极致画质可降至 `-crf 18` 或换 `-preset slow`。
