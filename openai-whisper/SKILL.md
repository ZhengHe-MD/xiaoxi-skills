---
name: openai-whisper
description: "Local speech-to-text with the Whisper CLI. Triggered when user sends a voice message or asks for transcription."
---

# Whisper.cpp (whisper-cli)

Transcribe audio to text using `whisper-cli`.

## Usage

### Step 1: Convert format (if needed)

Ogg (opus) from Feishu/Telegram needs conversion to wav:

```bash
ffmpeg -y -i /path/to/audio.ogg -ar 16000 -ac 1 -c:a pcm_s16le /tmp/voice_input.wav
```

### Step 2: Transcribe

```bash
whisper-cli -m ~/.cache/whisper/ggml-medium.bin /tmp/voice_input.wav -otxt -of /tmp/transcript -l zh
```

### Step 3: Read result

```bash
cat /tmp/transcript.txt
```

## Notes

- `-l zh` for Chinese, `-l auto` for auto-detect
- `-otxt` for plain text, `-osrt` / `-ovtt` for subtitles
- Ogg (opus) requires ffmpeg conversion, whisper-cli cannot read it directly
