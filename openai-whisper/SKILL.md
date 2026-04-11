---
name: openai-whisper
description: "本地语音转文字（ whisper.cpp / whisper-cli，Metal GPU 加速）。当用户发送语音消息或要求语音转录时触发。"
---

# Whisper.cpp (whisper-cli)

使用 `whisper-cli` 将语音转录为文字，利用 Apple Silicon Metal GPU 加速。

## 模型位置

已配置的模型（通过软链接）：
- `~/.cache/whisper/ggml-medium.bin` → `~/.local/share/whisper-cpp/models/ggml-medium.bin`

## 使用方法

### 基础转录

```bash
whisper-cli -m ~/.cache/whisper/ggml-medium.bin -f /path/to/audio.ogg -otxt -o /tmp
```

### 常用参数

- `-m <model_path>` ：模型路径（默认 `~/.cache/whisper/ggml-medium.bin`）
- `-f <audio_file>` ：音频文件路径（支持 flac、mp3、ogg、wav）
- `-otxt` ：输出为纯文本文件
- `-osrt` ：输出 SRT 字幕格式
- `-ovtt` ：输出 VTT 字幕格式
- `-l <lang>` ：指定语言，如 `-l zh`（中文，默认自动检测）
- `-t <threads>` ：线程数，默认 4
- `-bo <n>` ：beam size，默认 5

## 示例命令

```bash
# 转录中文音频，输出文本到 /tmp
whisper-cli -m ~/.cache/whisper/ggml-medium.bin -f /path/to/audio.ogg -otxt -o /tmp

# 指定语言为中文，输出 SRT
whisper-cli -m ~/.cache/whisper/ggml-medium.bin -f /path/to/audio.ogg -osrt -l zh -o /tmp
```

## 注意事项

- whisper-cli 由 `whisper.cpp` 提供，已通过 Homebrew 安装：`brew install whisper-cpp`
- Metal GPU 加速自动启用（MTL backend），无需额外配置
- 模型文件应放在 `~/.cache/whisper/` 或 `~/.local/share/whisper-cpp/models/`
- 如果模型路径有变动，检查软链接：`ls -la ~/.cache/whisper/ggml-medium.bin`
