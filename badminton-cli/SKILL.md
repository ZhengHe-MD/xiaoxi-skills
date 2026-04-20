---
name: badminton-cli
description: "Use the local badminton CLI to analyze badminton match videos, compile highlight videos, list or export individual rallies, and inspect match artifacts. Trigger when the user wants badminton video editing, rally clips, long-rally highlights, or match analysis from local video files."
---

Use the local `badminton` CLI for badminton video analysis and editing-style clip workflows.

## Command Location

- Prefer `badminton` directly.
- If PATH resolution is unreliable, use `/Users/zhenghe/.local/bin/badminton`.

## When To Use This Skill

Use this skill when the user wants to:

- analyze a badminton match video
- create a project from a local match recording
- compile a full highlight video with dead time removed
- make long-rally highlight videos
- list rallies and inspect their durations, scores, and tags
- export one video file per rally
- export rally timelines as YAML or CSV

## Default Workflow

### 1. Inspect current state first

If the user is not sure whether a project already exists:

```bash
badminton list
badminton inspect <project-name>
```

If the user only has a raw video path:

```bash
badminton inspect /path/to/match.mp4
```

### 2. Create a project if needed

```bash
badminton init /path/to/match.mp4 --name <project-name> --player-a "<name>" --player-b "<name>"
```

Projects are stored under:

```text
~/.local/share/badminton/<project-name>/
```

### 3. Run analysis

```bash
badminton analyze <project-name>
```

This runs:

- `probe`
- `proxy`
- `frame-sample`
- `game-detect`
- `rally-segment`
- `scoreboard-read`
- `event-detect`

### 4. Add score and rally metadata when needed

If the scoreboard is weak or absent, seed game winners first:

```bash
badminton score set-result <project-name> --game game-1 --winner a
badminton score set-result <project-name> --game game-2 --winner b
badminton score set-result <project-name> --game game-3 --winner b
```

Then tag:

```bash
badminton score tag <project-name>
badminton rally tag <project-name>
```

## Common User Jobs

### Compile a full highlight video

```bash
badminton compile <project-name> --out /absolute/path/output.mp4
```

### Compile long-rally highlights

Use duration as the current heuristic. Do not claim shot counting.

```bash
badminton compile <project-name> --min-dur 10 --out /absolute/path/long-rallies.mp4
```

### Compile tag-based highlights

```bash
badminton compile <project-name> --tag smash --out /absolute/path/smash-only.mp4
```

Multiple `--tag` flags use AND semantics.

### List rallies before exporting

```bash
badminton rally list <project-name>
badminton rally list <project-name> --game game-3 --min-dur 10
badminton rally list <project-name> --tag long
```

Use `rally list` before `export rallies` when the user wants to choose specific rally ids.

### Export individual rally clips

```bash
badminton export rallies <project-name> \
  --out-dir /absolute/path/rallies \
  --id rally-0002 \
  --pre-roll 0.5 \
  --post-roll 1.0
```

Useful filters:

```bash
badminton export rallies <project-name> --out-dir /absolute/path/rallies --game game-3
badminton export rallies <project-name> --out-dir /absolute/path/rallies --tag long
badminton export rallies <project-name> --out-dir /absolute/path/rallies --min-dur 10
```

Selection rules:

- multiple `--id` flags use OR semantics
- multiple `--tag` flags use AND semantics
- `--game`, `--tag`, and `--min-dur` intersect with each other

By default the export directory contains:

- one `<rally-id>.mp4` file per exported rally
- `manifest.csv`

Optional:

```bash
badminton export rallies <project-name> --out-dir /absolute/path/rallies --manifest-format yaml
```

### Export structured timelines

```bash
badminton export timeline <project-name> --format yaml
badminton export timeline <project-name> --format csv
```

## Operating Guidance

- Prefer absolute output paths for compiled videos and clip exports.
- Reuse existing projects when possible instead of creating duplicates.
- If a command says there are no rallies, run `analyze` first, then `score tag` and `rally tag` if the workflow depends on score or rally metadata.
- If the user asks for “editing”, map that to `compile` and `export rallies`, not timeline mutation.
- If the user asks for “long rallies”, use `--min-dur` or `--tag long` and explain that v1 uses duration, not shot counts.
- If a user wants one rally by identity, use `rally list` first, then `export rallies --id ...`.
