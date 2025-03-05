---
date: 2024-02-18
publish: true
slug: a2f21f02-edd8-4956-87a7-098edc267b7a
tags:
- CS/Backend
title: OpenSsh.md
---
## 交換openssh登入金鑰

- 必須先做一把RSA出來(我只用puttygen讀出來的格式試過)
- 公鑰放到遠端主機要登入使用者的家目錄 ~/.ssh/authorized_keys
- ~/.ssh/authorized_keys 必須是 600
- ~/.ssh 必須是 700
- 使用者級組別必須與登入名稱一致

以上在cent os7 及 6上試過

***

據說有指令的做法，要是有大大知道，告訴我一下

ref:https://askubuntu.com/questions/306798/trying-to-do-ssh-authentication-with-key-files-server-refused-our-key

## Make key On Linux

```sh
ssh-keygen [-t dsa | ecdsa | ecdsa-sk | ed25519 | ed25519-sk | rsa] [-C comment]
```

它會問你要存那跟密碼，如果不要密碼就留白

```sh
eval $(ssh-agent -s)
ssh-add ~/.ssh/prikey
```

啟動 ssh-agent 及把key 加入 ssh-agent
如果 `ssh-add -K ~/.ssh/prikey` 報錯就下 `ssh-add ~/.ssh/prikey`

## 連線測試

```sh
ssh -vT git@github.com
```
