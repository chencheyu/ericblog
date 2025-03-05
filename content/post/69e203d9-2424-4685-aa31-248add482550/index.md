---
date: 2024-07-08
publish: true
slug: b3e3b48f-c104-4741-a33e-f024d009b60d
tags:
- CS/Linux
title: Bash
uuid: 054603c2-88df-4b04-bc60-7bbbab193245
---
### 重導向

```bash
echo "echo" > log.txt
echo "echo" 2> log.txt
```

第一行會重導向 stdout，第二行會重導向 stderr

```bash
ls /dev 2>&1 | tee result.txt
```

把 stdout and stderr 複製到 result.txt 及銀幕
`tee -a result.txt` 附加在 result.txt 原內容後面

### Echo

```bash
echo "Date $(date)"
```

直接在 echo 中呼叫指令

```bash
echo "${var}"
```

顯示變數的值

```bash
echo -e "Hello\nworld"
echo -e 'Hello\nworld'
echo Hello$'\n'world
echo Hello ; echo world
```

換行，只有 bash 支援

```bash
printf "hello\nworld\n"
```

在 UNIX 上可攜

### File

```bash
tail -n 20000 -q sync.md > .sync.md
mv .sync.md sync.md
```

如果 sync.md 大於 20000 行則將檔頭截斷否則保留原始檔案

### 變數

```bash
SyncRlt="$?"
RltFile="r.log"
echo "SyncRlt ${SyncRlt}" > ${RltFile}
```

型態只有字串，等號兩側不可以有空格
`${var}` 可以使用變數，會在執行該行前被替換

### Debug

```bash
bash +x sync.bash
bash --verbose sync.bash
```

### Array

```
array=(1,2,3,4)
array=(1 2 3 4)
echo ${arary[@]}
```

定義跟顯示

```bash
for str in ${myArray[@]}; do
  echo $str
done
```

尋訪

```bash
for i in ${!myArray[@]}; do
  echo "element $i is ${myArray[$i]}"
done
```

索引尋訪

### [Condition](https://www.gnu.org/software/bash/manual/html_node/Bash-Conditional-Expressions.html)

```sh
if [ <some test> ]
then
	<commands>
fi

if [ <some test> ];then
	<commands>
else
	<commands>
fi
```

`&lt;some test&gt;`

- `! EXPRESSION` The EXPRESSION is false.
- `-n STRING` The length of STRING is greater than zero.
- `-z STRING` The lengh of STRING is zero (ie it is empty).
- `STRING1 = STRING2` STRING1 is equal to STRING2
- `STRING1 != STRING2` STRING1 is not equal to STRING2
- `INTEGER1 -eq INTEGER2` INTEGER1 is numerically equal to INTEGER2
- `INTEGER1 -gt INTEGER2` INTEGER1 is numerically greater than INTEGER2
- `INTEGER1 -lt INTEGER2` INTEGER1 is numerically less than INTEGER2
- `-d FILE` FILE exists and is a directory.
- `-e FILE` FILE exists.
- `-f file` True if file exists and is a regular file.
- `-r FILE` FILE exists and the read permission is granted.
- `-s FILE` FILE exists and it's size is greater than zero (ie. it is not empty).
- `-w FILE` FILE exists and the write permission is granted.
- `-x FILE` FILE exists and the execute permission is granted.

> [!note]
> `&lt;some test&gt;` 接受字串後轉變為 Operator 需要的型別
> if 與 `[` 間的空白是必要的



```bash
if [[ $num -eq 3 && "$stringvar" == foo ]]; then

fi
```

`&amp;&amp;`, `||` 只在 if 使用 `[[]]` 時可用

if 接受一個命令並運行它，並使用該命令的返回值來決定如何繼續，零表示 true，非零表示 false

```sh
eric@16AHP9:~$ [ 0 -eq 0  ]
eric@16AHP9:~$ echo $?
0
eric@16AHP9:~$ [ 0 -eq 1  ]
eric@16AHP9:~$ echo $?
1
```

`[&lt;some test&gt;]` 也是一個命令（以前，現在由 bash 內建），估值 `&lt;some test&gt;` 成功返回 0，失敗返回 1

### Command Substitution

```sh
file $(which bash)
```

命令替換允許命令的輸出替換命令本身

### New Line

如果 bash file 用了 windows style(`\n\r`) 的換行，可能導致 `unexpected end of file`

### Function

```bash
greeting () {
  echo "Hello $1"
}

greeting "Joe"
```

### List Directory

```sh
search_dir=/the/path/to/base/dir
for entry in "$search_dir"/*
do
  echo "$entry"
done
```

### Bash Init

- `~/.bash_profile` 為登入 shell 運行
- `~/.bashrc` 為非互動式shell運行
  有設定可能會把 bash_profile 將 BASH_ENV 設定為指向 .bashrc，[可能引起無限遞迴](https://github.com/pyenv/pyenv/issues/264)，強烈不建議
- `/etc/profile.d/` 資料夾下的檔案為系統上全部 shell 登入前運行

### EOF

```bash
cat > sim.conf << EOF
conf-0 : 0
conf-1 : 2
conf-2 : str
EOF
```

將 EOF 中間的字串視為多行字串覆寫進 sim.conf

### exec

```bash
exec bash sync.bash
```

> Replace the shell with the given command.
> Execute COMMAND, replacing this shell with the specified program. ARGUMENTS become the arguments to COMMAND.  If COMMAND is not specified, any redirections take effect in the current shell.



> [!note]
> 不可以用 `'` 把要替換的指令包起來
> `exec 'bash sync.bash'` 會導致 `bash: exec: bash sync.bash: not found`



exec 會呼叫 FD_CLOEXEC 關閉在 exec 前開啟的檔案，但不會關閉其他資源

### Gerp in Condition

```bash
if grep --quiet MYSQL_ROLE=master /etc/aws/hosts.conf; then
  echo exists
else
  echo not found
fi
```

> From `grep --help`
> 若選擇任何行，退出狀態為 0，否則為 1；如果發生任何錯誤且未給予 -q，則退出狀態為 2



### `sh: 0: getcwd() failed: No such file or directory`

在 dash 中 `cd ~` 解

```sh
eric@16AHP9:~$ mkdir /tmp/z
eric@16AHP9:~$ cd /tmp/z
eric@16AHP9:/tmp/z$ rm -r /tmp/z
eric@16AHP9:/tmp/z$ /bin/bash
shell-init: error retrieving current directory: getcwd: cannot access parent directories: No such file or directory
sh: 0: getcwd() failed: No such file or directory
```

問題主應是 dash 不喜歡不存在的工作目錄
此問題會同時導致其他依賴 `/bin/sh` 的指令失效如 pyenv, egrep
