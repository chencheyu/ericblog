---
date: 2025-01-02
publish: true
slug: fe4f432a-c5e2-4118-89b1-32cd71d66eda
tags:
- CS/Ai
title: Llam Cpp
uuid: 5b897ca2-ed03-461b-9cff-9c407f362805
---
### Build

```sh
curl -L https://huggingface.co/bartowski/Llama-3.2-1B-Instruct-GGUF/resolve/main/Llama-3.2-1B-Instruct-IQ3_M.gguf?download=true -o Llam-3.2-1B.gguf
```

下載 Llam 模型

#### AMD CPU

```sh
git clone --depth 1 https://github.com/ggerganov/llama.cpp.git
cmake -Bbuild
cd build/
make -j8
```

```sh
bin/llama-cli -m Llam-3.2-1B.gguf -p "Hi" -n 100
```

### Vulkan AMD

```sh
sudo apt install vulkan-tools libvulkan-dev glslc

bin/llama-cli -m Llam-3.2-1B.gguf -p "Hi" -n 100 -ngl 1000
```
