---
title: "インストール手順"
---

ここでは、GitLab CEのインストール手順について述べる。


## インストール方法

GitLabのインストール手段として、大きくわけて2つの方法が挙げられる。

* Omnibus パッケージを利用したインストール方法
* すでにOmnibus パッケージが適用されたDocker イメージを展開する方法


Omnibus パッケージとは、GitLab CE のコアアプリケーションに加え、それらを稼働するのに必要なアプリケーションとミドルウェアをパッケージ化したものである。
これを利用することによって面倒な設定をせずに、かんたんな操作のみでインストールを行うことができる。

Omnibus パッケージは、GitLab CEのみならず、GitLab EEにおいても提供されており、GitLab の公式のドキュメントとしては、Omnibus パッケージを利用が強く推奨している。  
そのためOmnibus パッケージを利用したインストールによる方法を取り扱う。

Omnibus パッケージを利用したインストールは、[Install self-managed GitLab](https://about.gitlab.com/install/)に従えばよい。  
今回はUbuntuが対象のため、その記述をベースにインストール作業を行う。


## インストール作業

[Install GitLab with the Linux package – GitLab Docs](https://docs.gitlab.com/omnibus/installation/)をベースに実施。

### 事前準備

* sshサーバは事前に設定しておくものとする。
* メール機能を用いる場合はSMTPの設定(Postfix etc)の設定は済ませておくものとする。

### debパッケージのインストール

必要なdebパッケージをインストールする。

```bash:terminal
sudo apt update
sudo apt install -y curl openssh-server ca-certificates tzdata perl
```

### debパッケージのインストールと初回起動

GitLabパッケージをインストールする。  
ここではGitLab CE（コミュニティエディション）をインストールする。

```bash:terminal
curl https://packages.gitlab.com/install/repositories/gitlab/gitlab-ce/script.deb.sh | sudo bash
sudo apt install -y gitlab-ce
```

GitLabの初回起動を行う。  
そのためには、`gitlab-ctl`コマンドを介して再構成を行う必要がある。

```bash:terminal
sudo gitlab-ctl reconfigure
```

### GitLabへのログイン

初回起動が無事に完了したら、ログインを行うが、その前にログインのための初期パスワード確認する。

```bash:terminal
sudo cat /etc/gitlab/initial_root_password
```

以下の通り表示されるはずである。

```
# WARNING: This value is valid only in the following conditions
#          1. If provided manually (either via `GITLAB_ROOT_PASSWORD` environment variable or via `gitlab_rails['initial_root_password']` setting in `gitlab.rb`, it was provided before database was seeded for the first time (usually, the first reconfigure run).
#          2. Password hasn't been changed manually, either via UI or via command line.
#
#          If the password shown here doesn't work, you must reset the admin password following https://docs.gitlab.com/ee/security/reset_user_password.html#reset-your-root-password.

Password: abc123!abc123ABC123!abc123abc123ABC123!

# NOTE: This file will be automatically deleted in the first reconfigure run after 24 hours.
```

上記の場合、初期パスワードは`abc123!abc123ABC123!abc123abc123ABC123!`となる。

ブラウザより `http://<HOST_IP_ADDR>` にアクセスする。
(`<HOST_IP_ADDR>` はインストールしたサーバーのIPアドレス or ドメイン名)

ログイン画面が表示されるので、ユーザ名に`root`、パスワードに先ほど確認した初期パスワードを入力し、"サインインする"をクリックすると、ログインが完了となる。

![ログイン画面](/images/98e421ccc894a9/install_01.png)


### 初期パスワードの変更

ここではrootでログインを行ったが、rootの初期パスワードの有効期限は24時間であることから、パスワードの変更を行う必要がある。  
左上アバターマークをクリックし、"Edit Profile" をクリックする。

![Edit Profile](/images/98e421ccc894a9/install_02.png)

左のペインの"Password"をクリックすると、パスワード変更画面が表示されるので、パスワードの変更を行う。
