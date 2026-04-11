---
name: openai-whisper
description: "本地语音转文字。当用户发送语音消息或要求语音转录时触发。"
---

# Whisper.cpp (whisper-cli)

使用 `whisper-cli` 将语音转录为文字。

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

### Step 3：读取结果

```bash
cat /tmp/transcript.txt
```

## Notes

- `-l zh` 指定语言，`-l auto` 自动检测
- `-otxt` 输出纯文本，`-osrt` / `-ovtt` 输出字幕
- ogg（opus 编码）需 ffmpeg 转 wav，whisper-cli 无法直接读取
