---
date: 2025-02-15
publish: true
slug: f8ddc275-6f3b-4117-88b5-2b14527ccbb4
tags:
- CS/Linux
title: Shell Script Compiler
uuid: 5477a95c-ea7d-4ff5-b391-88cdd2fbbcb2
---
[shc](https://github.com/neurobin/shc)

> 一個通用的 shell 腳本編譯器。Shc 接受一個在命令列中指定的腳本，並產生 C 原始碼。生成的原始碼隨後會被編譯並鏈接，以產生一個去除符號表的二進位可執行文件。
> 編譯後的二進位文件仍然依賴於 shell 腳本第一行中指定的 shell（即 shebang，例如 #!/bin/sh），因此 shc 無法創建完全獨立的二進位文件。
> Shc 本身並不是一個像 cc 那樣的編譯器，而是對 shell 腳本進行編碼和加密，並生成具有到期功能的 C 原始碼。之後，它使用系統的編譯器來編譯出一個去除符號表的二進位文件，該文件的行為與原始腳本完全相同。在執行時，編譯後的二進位文件將解密並使用 shell 的 -c 選項來執行代碼。

> ### Usage



```sh
shc -f script.sh -o binary
```

## [Shell Script Compiler無法保護你的腳本](https://hackmd.io/@kmo/shc_not_safe)

> [!warning] 危險行為
> 撰寫了一個 shell script，裡面包含 API 金鑰等機敏資訊
> 為了避免一般用戶讀到腳本內的 API 金鑰等機敏內容，所以使用 shc 轉成 binary，看似一般人讀不到腳本內容，就放在 production 環境



> Note that it doesn't work with bash (or sh based on bash), as one can do: env SHELLOPTS=verbose ./script.x to see the content of the script. There's probably easy ways to bypass with other shells as well.
> - [How to convert a shell script into a binary executable](https://unix.stackexchange.com/questions/64762/how-to-convert-a-shell-script-into-a-binary-executable/64765) @ [Calmarius](https://unix.stackexchange.com/users/26435/calmarius)



```sh
#!/bin/bash
unset SHELLOPTS
echo "Hello World"
echo "At $(pwd)"
```

建立 hello.sh

```sh
eric$ shc -f hello.sh -o hello 
eric$ file hello
hello: ELF 64-bit LSB pie executable, x86-64, version 1 (SYSV), dynamically linked, interpreter /lib64/ld-linux-x86-64.so.2, BuildID[sha1]=55f83777c52f6f496c169094ae232b7b23ec4b61, for GNU/Linux 3.2.0, stripped
eric$ nm hello
nm: hello: no symbols
eric$ ldd hello
	linux-vdso.so.1 (0x00007ffc5eac9000)
	libc.so.6 => /lib/x86_64-linux-gnu/libc.so.6 (0x00007633f5000000)
	/lib64/ld-linux-x86-64.so.2 (0x00007633f53c3000)
```

編譯並確定他是 elf，沒有 symbols

```sh
eric$env SHELLOPTS=verbose ./hello
                #!/bin/bash

unset SHELLOPTS
./hello: line 3: unset: SHELLOPTS: cannot unset: readonly variable
echo "Hello World"
Hello World
echo "At $(pwd)"
At /home/eric/Downloads
```

雖然排版亂了，但還是看到了 code
由於 [SHELLOPTS](https://www.gnu.org/software/bash/manual/html_node/Bash-Variables.html#index-SHELLOPTS) 是 readonly 變數，即使在腳本裡面嘗試unset SHELLOPTS去反制，也無法阻擋SHELLOPTS=verbose效果

```sh
strace -s 10000000 -v ./hello 2>&1 |  grep bash
```

用 strace 也可以看到要給 bash 跑的指令，雖然比 SHELLOPTS=verbose 亂的多

```sh
shc -U -f hello.sh -o hello
```

官方表示 `-U` 可以被免被 strace 追蹤，但 202502 測試時會返回 `./hello: Operation not permitted` 無法使用
