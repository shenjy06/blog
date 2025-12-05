---
title: Linux 中使用命令行播放视频
date: 2025-11-12 19:30:51
tags:
  - Linux
---

## Linux OS 中如何使用命令来播放 mp4

prompt> how to use linux command to play mp4 video?

DeepSeek-v3:
To play an MP4 video in Linux using the command line, you can use several command-line video players. Here are some popular options:

### 1. **`mpv` (Recommended)**

- A versatile and popular media player with minimal GUI.
- Install (Debian/Ubuntu):
  ```bash
  sudo apt install mpv
  ```
- Play video:
  ```bash
  mpv your_video.mp4
  ```
- Options:
  - `--no-video` → Play audio only.
  - `--fs` → Fullscreen mode.

### 2. **`vlc` (VLC Command-Line)**

- VLC can also be controlled via the command line.
- Install (Debian/Ubuntu):
  ```bash
  sudo apt install vlc
  ```
- Play video:
  ```bash
  vlc your_video.mp4
  ```
- Options:
  - `--fullscreen` → Fullscreen playback.
  - `--no-video` → Audio only.

### 3. **`ffplay` (FFmpeg’s Player)**

- A simple player from the `ffmpeg` suite.
- Install (Debian/Ubuntu):
  ```bash
  sudo apt install ffmpeg
  ```
- Play video:
  ```bash
  ffplay your_video.mp4
  ```
- Options:
  - `-fs` → Fullscreen mode.
  - `-nodisp` → Play audio without displaying video.

### 4. **`mplayer` (Legacy Player)**

- A classic media player.
- Install (Debian/Ubuntu):
  ```bash
  sudo apt install mplayer
  ```
- Play video:
  ```bash
  mplayer your_video.mp4
  ```
- Options:
  - `-vo null` → Disable video (audio only).
  - `-fs` → Fullscreen.

### Example with Controls:

- In `mpv`/`mplayer`/`ffplay`, you can pause/resume with **Space**, quit with **q**, and adjust volume with **↑/↓** keys.

If you prefer a **completely terminal-based** experience (no GUI), you could even use ASCII playback (with `mpv` in terminal mode):

```bash
mpv --vo=caca your_video.mp4
```

_(Uses ASCII art for video output—mostly for fun.)_

Let me know if you want advanced options (like streaming, subtitles, or playback control)!

prompt> quit

Exiting AI mode.
