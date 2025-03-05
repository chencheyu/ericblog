---
date: 2025-01-16
publish: true
slug: 6ec15d54-4c04-4511-b758-97d934f5fe55
tags:
- CS/Ai
title: whisper
uuid: 72a1518a-e723-436e-ac48-872d693b9c7c
---
## [Open Ai Whisper](https://github.com/openai/whisper)

Whisper 是一種通用語音辨識模型。它是在大量不同音訊資料集上進行訓練的，也是一個多任務模型，可以執行多語言語音識別、語音翻譯和語言識別。
Open Ai 用 Pytorch 實做的語音轉文字的實做，多語言效果良好

```sh
pip install -U openai-whisper
pipx install openai-whisper
```

```sh
whisper --model tiny --language Chinese  ~/Downloads/untitled.mp3
```

model 有以下值可選

- tiny	        Parameter 39M
- base	Parameter 74M
- small	Parameter 244M
- medium	Parameter 769M
- large	Parameter 1550M
- turbo	Parameter 809M

另有 tiny.en base.en small.en medium.en 只支援英文的模型

## [`Whisper.cpp`](https://github.com/ggerganov/whisper.cpp)

看著 [Open Ai Whisper](https://github.com/openai/whisper) 寫的 Cpp 實做，快的多

```sh
cmake --build build -j 8 --config Release
cd build
make
./bin/whisper-cli -m /home/eric/whisper.cpp/models/ggml-tiny.bin -f  ../samples/jfk.wav
```

只有 CPU 運算

```sh
apt install libavformat-dev libavformat-dev libavutil-dev libavformat-extra libavtp0
```

加入 ffmpeg

### Vulkan

## [faster-whisper](https://github.com/SYSTRAN/faster-whisper)

Cpp 實做，降低浮點數精度
