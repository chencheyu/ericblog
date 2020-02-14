---
title: openssh key
date: 2020-02-11 21:17:12
tags: backend
permalink: 1581427032718
---
# 交換openssh登入金鑰

- 必須先做一把RSA出來(我只用puttygen讀出來的格式試過)
- 公鑰放到遠端主機要登入使用者的家目錄 ~/.ssh/authorized_keys
- ~/.ssh/authorized_keys 必須是 600
- ~/.ssh 必須是 700
- 使用者級組別必須與登入名稱一致

以上在cent os7 及 6上試過

-------
據說有指令的做法，要是有大大知道，告訴我一下

ref:https://askubuntu.com/questions/306798/trying-to-do-ssh-authentication-with-key-files-server-refused-our-key