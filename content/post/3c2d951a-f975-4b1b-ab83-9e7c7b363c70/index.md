---
date: 2024-10-15
publish: true
slug: 2a8d5903-c184-46ca-ab80-9109293a5eec
tags:
- CS/Python
title: Version Management
uuid: 93930e64-21f4-45a4-ab8a-57aaa7018f12
---
## py

Python Launcher for Windows

```sh
py --list
py -V3:11
```

啟動新版本要用 cmd，用 windows 版的 bash 會 hang
新 python 版本不能用 py 裝，要去 python 官網下載安裝包
路徑在 `c:Windows\py`

## [pyenv](https://github.com/pyenv/pyenv)

> pyenv 讓您輕鬆地在多個 Python 版本之間切換。它簡單、不引人注目，並且遵循 UNIX 單一用途工具只做好一件事的傳統。
> 技術上是個 bash 變數墊片，會抓 python 在本地 build



```bash
git clone https://github.com/pyenv/pyenv.git ~/.pyenv
echo 'export PYENV_ROOT="$HOME/.pyenv"' >> ~/.bashrc
echo 'command -v pyenv >/dev/null || export PATH="$PYENV_ROOT/bin:$PATH"' >> ~/.bashrc
echo 'eval "$(pyenv init -)"' >> ~/.bashrc
```

大概是 clone pyenv 到 ~/.pyenv 在加 PYENV_ROOT 到環境變數
只能為每一個使用者單獨安裝，sudo 會裝去 /root

#### Install For All User

[Step-by-Step Guide: Setting Up pyenv](https://vishwakarmarohit.medium.com/pyenv-ec2d9629e86f)

```sh
apt install --yes libssl-dev zlib1g-dev libbz2-dev libreadline-dev libsqlite3-dev
apt install --yes xz-utils tk-dev libxml2-dev libxmlsec1-dev libffi-dev liblzma-dev libncursesw5-dev 
git clone --depth 3 https://github.com/pyenv/pyenv.git /usr/share/pyenv
cat > /etc/profile.d/pyenv.sh << EOF
export PYENV_ROOT="/usr/share/pyenv"
command -v pyenv >/dev/null || export PATH="$PYENV_ROOT/bin:$PATH"
eval "$(pyenv init -)"
EOF
chmod 777 -R /usr/share/pyenv
chmod 755 /etc/profile.d/pyenv.sh
```

```sh
pyenv install --list
```

列出所有能安裝的版本

```sh
pyenv versions
```

列出以安裝的版本

> 如果您是虔誠的 virtualenv 或 venv 用戶，請不要擔心：pyenv 與兩者都能很好地協作。事實上，如果您願意，可以保持相同的工作流程
> - [Managing Multiple Python Versions With pyenv](https://realpython.com/intro-to-pyenv/)



## Pip

使用 `python3.X -m pip` 而不是 `pip install`：為避免當使用者擁有多版 Python 版本時，使用自己不明確的 pip 版本

### Requirement

通常拿來保存專案的包相依性

```sh
python -m pip install -r /path/to/requirements.txt
```

安裝

```sh
python -m pip freeze > requirements.txt
```

產生

## Venv

systemd 可以直接執行 Venv 創造出的 python 不需要載入環境變數

## [pipx](https://github.com/pypa/pipx)

Install and Run Python Applications in Isolated Environment
pipx 為每個應用程式及其關聯的套件建立一個隔離的環境

```sh
pipx install --global pycowsay
```

將應用程式公開給所有的系統使用者
