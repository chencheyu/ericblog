---
date: 2023-02-20
publish: true
slug: baa52eac-c9c9-4384-9475-c9c0be5767e1
tags:
- CS
title: Git.md
---
### Git Server

```
git init --bare
```

會產生一個沒有工作目錄的 git repo

```
git clone ssh://UserName@host.com:port/path/to/repo
```

用 ssh 拉遠端機器的git repo回來

```
git clone /port/path/to/repo
```

git local clone

### File Permission

如果在 git 中放可執行檔，git 可能不會正確給予可執行權限，導致每次 pull 都會改變權限

```
git ls-tree HEAD
```

```
100644 blob 03d73c7753ad8624326cc21c84f4bf6cff665111    .gitignore
100644 blob 28ad11c0d207a9cef0b588eb1eb1925eb9aa0e0c    .gitmodules
100755 blob ab6b28bd6514f4ea3d39a3361c9461ba5e4752dc    README.md
040000 tree e872376a7169a3d136aa9e82b8755765e1393a04    etc
040000 tree 26431089c1aaa47c277ffe8363c66e7cd54d9abe    prvl
160000 commit 1772dafe7241dcc84a710f2b59d3f53eceae1afb  quote_generate
040000 tree 5e754b8b1284727eb640080a04d07199425df7c0    usr
```

他會列出檔案跟資料夾的 hash，資料夾可以用 hash 繼續爬樹

```
git update-index --chmod=+x script.sh
```

用檔名改權限， hash 沒用權限只能+x, -x 數字沒用

### 登入權杖

在某些機器上 git 預設不存登入權杖，下面的命令可以使儲存庫儲存登入權杖，登入權杖在 Linux 預設明文存在`~/.git-credentials`

```bash
git config --global credential.helper store
```

快取登入權杖，避免它存在磁碟上

```bash
git config --global credential.helper 'cache --timeout=3600'
```

### Submodule

```bash
git config submodule.my-submodule.url "https url for the submodule"
```

更改 Submodule URL

### 歷史

#### [Git 的故事：這一次沒這麼好玩](https://blog.brachiosoft.com/posts/git/)

Git 起源歷史的詳細文章

### Branch

```bash
git checkout -b <branch-name>
```

將跟蹤分支設為 branch-name 如果不存在就創建

#### 移動分支到指定 Commit

```bash
git switch -C <branch-name> [<start-point>]
```

強制移動分支到指定 commit ，`-C` C 大寫分支必須存在，`-c` c 小寫分支必須不存在(Git 2.23.0 版以上)

```bash
git reset --hard <new-tip-commit>
```

將現在指向的分支移動到 new-tip-commit，但實際操做時有可能斷頭

#### gitignore

```sh
git clean -fX
```

清除 gitignore 中包含的檔案
[github/gitignore](https://github.com/github/gitignore) gitignore 範本集合

```
*.a # ignore all .a files
!lib.a # but do track lib.a, even though you're ignoring .a files above
/TODO  # only ignore the TODO file in the current directory, not subdir/TODO
build/ # ignore all files in any directory named build
doc/*.txt    # ignore doc/notes.txt, but not doc/server/arch.txt
doc/**/*.pdf # ignore all .pdf files in the doc/ directory and any of its subdirectories
```

[Pattern Matching](https://www.gnu.org/software/bash/manual/html_node/Pattern-Matching.html)
正斜線 ( `/`) 開始模式以避免遞歸
正斜線 ( `/`) 結束模式來指定目錄

### Configuration

```sh
git config --list
```

列出 .git/config 的設定值

```sh
git config --list --show-origin
```

列出所有 git 的設定值及設定該值的設定檔

### Clone

```sh
git clone --depth 5 https://github.com/torvalds/linux.git
```

限制最深 clone 的樹層數，減少 clone 的資料量(大庫不需要歷史紀錄時很好用)

### Git Revisions

指定 Git 的修訂版本和範圍

```sh
git diff HEAD..84dd02d464488a58c177271846157b45e7d587b6 
```

指定範圍，HEAD 到特定 commit

```sh
git show HEAD^2
```

HEAD 前兩個節點

```sh
git show <refname>
```

`&lt;refname&gt;`，例如 master、Heads/master、refs/heads/master
一個象徵性的參考名稱，以下格式有效(包括但不限於)

- `refs/tags/&lt;refname&gt;` Tag 名稱
- `refs/heads/&lt;refname&gt;` 本地分支名稱
- `refs/remotes/&lt;refname&gt;`  遠端分支名稱

### File Change History

```sh
gitk <filename>
gitk --follow <filename>
git log --follow -p <filename>
```

### Gitlab

#### Access Token

2023 年後如果要用 HTTPS  協議 clone gitlab 上的專案，不能再用 gitlab 的登入密碼作為權杖，要為每一個專案創造一個 Access Token(在 Settings > Access Tokens)，並用以下 url clone 專案

```
git clone https://<your-username>:<your-pat>@gitlab.com/<your-username>/<your-repository>.git
```

如果登入時被問密碼，給 your-pat

#### 關聯 SSH 金鑰

貼上公鑰時要注意最後是否有空格
