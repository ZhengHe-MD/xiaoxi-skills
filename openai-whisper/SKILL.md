---
name: openai-whisper
description: "本地语音转文字（ whisper.cpp / whisper-cli，Metal GPU 加速）。当用户发送语音消息或要求语音转录时触发。"
---

# Whisper.cpp (whisper-cli)

使用 `whisper-cli` 将语音转录为文字，利用 Apple Silicon Metal GPU 加速（比纯 CPU 快 20-50 倍）。

## 模型准备

模型位于 `~/.local/share/whisper-cpp/models/ggml-medium.bin`，需要软链接到 whisper-cli 期望的位置：

```bash
mkdir -p ~/.cache/whisper
ln -sf ~/.local/share/whisper-cpp/models/ggml-medium.bin ~/.cache/whisper/ggml-medium.bin
```

## 使用方法

### Step 1：转换格式（如需要）

飞书/Telegram 发来的 ogg（opus 编码）需要转 wav：

```bash
ffmpeg -y -i /path/to/audio.ogg -ar 16000 -ac 1 -c:a pcm_s16le /tmp/voice_input.wav
```

### Step 2：转录

```bash
whisper-cli -m ~/.cache/whisper/ggml-medium.bin /tmp/voice_input.wav -otxt -of /tmp/transcript -l zh
```

- `-m` 模型路径
- 音频文件放最后（positional argument）
- `-otxt` 输出纯文本
- `-of` 输出文件路径（不带扩展名）
- `-l zh` 指定中文，`-l auto` 自动检测语言

### Step 3：读取结果

```bash
cat /tmp/transcript.txt
```

## 常用参数

| 参数 | 说明 |
|---|---|
| `-l <lang>` | 语言，如 `zh`、`en`、`auto` |
| `-osrt` | 输出 SRT 字幕 |
| `-ovtt` | 输出 VTT 字幕 |
| `-oj` | 输出 JSON |
| `-bo <n>` | beam size，默认 5 |
