---
name: openai-whisper
description: "本地语音转文字（ whisper.cpp / whisper-cli，Metal GPU 加速）。当用户发送语音消息或要求语音转录时触发。使用 OpenAI Whisper (Python) 时触发。"
---

# Whisper.cpp (whisper-cli)

使用 `whisper-cli` 将语音转录为文字，利用 Apple Silicon Metal GPU 加速。

## 性能参考

- 2.9 秒音频：推理耗时 ~1.7 秒（不含模型加载）
- 模型加载（medium）：~400ms
- 对比 OpenAI Whisper（纯 CPU）：~80 秒同一段音频
- **提速约 20-50 倍**

## 模型路径

已配置的模型（通过软链接）：
- `~/.cache/whisper/ggml-medium.bin` → `~/.local/share/whisper-cpp/models/ggml-medium.bin`

如果软链接不存在，先创建：
```bash
mkdir -p ~/.cache/whisper
ln -sf ~/.local/share/whisper-cpp/models/ggml-medium.bin ~/.cache/whisper/ggml-medium.bin
```

## 完整转录流程

### Step 1：转换格式（如需要）

飞书/Telegram 发来的 ogg（opus 编码）whisper-cli 无法直接读取，需用 ffmpeg 转 wav：

```bash
ffmpeg -y -i /path/to/audio.ogg -ar 16000 -ac 1 -c:a pcm_s16le /tmp/voice_input.wav
```

**支持的输入格式**：flac、mp3、ogg（部分）、wav
**ffmpeg 转码后支持**：所有格式

### Step 2：转录

```bash
whisper-cli -m ~/.cache/whisper/ggml-medium.bin -f /tmp/voice_input.wav -otxt -of /tmp/transcript -l zh
```

- `<file>` 放最后（positional argument，不是 `-f`）
- `-of` 指定输出文件路径（不带扩展名），不是 `--output_format`
- `-otxt` 输出纯文本
- `-l zh` 指定中文（也可 `-l auto` 自动检测）

### Step 3：读取结果

```bash
cat /tmp/transcript.txt
```

## whisper-cli vs whisper（OpenAI Python）

| | whisper-cli (cpp) | whisper (Python) |
|---|---|---|
| 加速 | Metal GPU | 纯 CPU |
| 速度 | ~2秒/3秒音频 | ~80秒/3秒音频 |
| ogg 支持 | 需 ffmpeg 转换 | 原生支持 |
| 模型格式 | ggml（.bin） | safetensors（.pt） |

## 常用参数

| 参数 | 说明 |
|---|---|
| `-m <path>` | 模型路径 |
| `<audio_file>` | 音频文件（positional） |
| `-otxt` | 输出 TXT |
| `-osrt` | 输出 SRT 字幕 |
| `-ovtt` | 输出 VTT 字幕 |
| `-oj` | 输出 JSON |
| `-l <lang>` | 语言，如 `zh`、`en`、`auto` |
| `-of <path>` | 输出文件路径（不带扩展名） |
| `-bo <n>` | beam size，默认 5 |
| `-t <n>` | 线程数，默认 4 |

## 故障排除

**"failed to read audio data as wav"**
→ ogg/opus 格式不兼容，用 ffmpeg 转 wav（见 Step 1）

**模型加载很慢**
→ 首次运行需加载 ~1.5GB 模型文件，正常

**GPU 未被使用**
→ whisper-cli 自动检测 Metal，确认日志中有 `MTL0` 即可

## 注意事项

- whisper-cli 由 `whisper.cpp` 提供，已通过 Homebrew 安装：`brew install whisper-cpp`
- Metal GPU 加速自动启用（MTL backend），无需额外配置
- 如果要指定其他模型，修改 `-m` 参数即可
