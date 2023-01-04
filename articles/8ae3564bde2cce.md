---
title: "Ubuntuにpyenvをインストール"
emoji: "👋"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["Python"]
published: true
---

# はじめに

基本的にはUbuntu向けのインフラの備忘録兼自分のためのリンク用。

# Python環境のインストールについて

UbuntuへのPythonをインストールするにあたり、考えられる主なアプローチとしては、以下がある。

- PPAリポジトリよりaptでインストールする。
- 自前でビルドする。
- pyenvを用いる。

このなかでpyenvがもっともインストールおよび操作が容易であることから、pyenvによるインストールを採用し、関連する作業について記載を行う。

pyenvのドキュメントはgithubのみに存在する。

- pyenv github  
  [https://github.com/pyenv/pyenv](https://github.com/pyenv/pyenv)


# pyenvのインストール

まずはpyenv自体をインストールする。


pyenvインストールに必要なパッケージをインストールする。

```bash:terminal
sudo apt update
sudo apt install build-essential libffi-dev libssl-dev zlib1g-dev liblzma-dev libbz2-dev \
  libreadline-dev libsqlite3-dev libopencv-dev tk-dev git
```

githubより、pyenvのクローンを行う。

```bash:terminal
git clone https://github.com/pyenv/pyenv.git ~/.pyenv
```

`.bashrc`にpyenvの情報を追記し、`.bashrc`の再読み込みを行う。  
※ここではログインシェルはbashという前提のもと記載している。  
zshの場合は適宜置き換えること。

```bash:terminal
echo '' >> ~/.bashrc
echo 'export PYENV_ROOT="$HOME/.pyenv"' >> ~/.bashrc
echo 'export PATH="$PYENV_ROOT/bin:$PATH"' >> ~/.bashrc
echo 'eval "$(pyenv init --path)"' >> ~/.bashrc
source ~/.bashrc
```

pyenvを実行することができることを確認する。

```bash:terminal
pyenv -v 
```

# PyenvによるPython環境導入

## インストール可能なpythonバージョン一覧を表示

```bash:terminal
pyenv install --list
```

ここで表示されたバージョンがPythonとしてインストール可能なバージョンとなる。

## pythonのインストール

例えば、`version 3.10.8`をインストールする場合は、以下の通り入力する。

```bash:terminal
pyenv install 3.10.8
```

## インストール済みのpyenvのバージョン確認

```bash:terminal
pyenv versions
```

# Python環境の適用

## カレントディレクトリにて使用されるPythonのバージョンを確認

```bash:terminal
pyenv version
```

## 全体(デフォルト)で利用するバージョンの設定

環境全体のPythonのバージョンを`3.X.Y`になる。

```bash:terminal
pyenv global 3.X.Y
```

例えば環境全体のPythonのバージョンを`3.10.8`にしたい場合は、以下を入力する。

```bash:terminal
pyenv global 3.10.8
```

## 特定のディレクトリ配下のみ利用するバージョンの設定

このコマンドを実行したディレクトリ配下のPythonのバージョンは`3.X.Y`になる。

```bash:terminal
pyenv local 3.X.Y
```

例えば`/opt/project`以下のPythonのバージョンを`3.8.10`としたい場合は、
`/opt/project`に移動した後、`pyenv local 3.8.10`を実行する。

```bash:terminal
cd /opt/project
pyenv local 3.8.10
```
