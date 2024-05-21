---
title: "バックアップ・リストア"
---

ここではバックアップ・リストアについて取り扱う。  
大前提として、作成されたバックアップは、同じバージョンのGitLab にのみ展開可能である。
すなわち、GitLabのバージョンアップが前提とする場合は、そのための手順を実行する必要がある。

なお、インストールされたパッケージのバージョンは、以下のコマンドにて確認することができる。

```bash:terminal
apt list --installed gitlab-ce
```

```
Listing... Done
gitlab-ce/jammy,now 17.0.0-ce.0 amd64 [installed]
N: There are 153 additional versions. Please use the '-a' switch to see them.
```

この場合は、`17.0.0-ce.0`というバージョンがインストールされていることがわかる。


## バックアップの取得

GitLabのアプリケーションデータのバックアップは、`gitlab-rake`コマンドにより実装されている。

以下のコマンドにより、すべてのオブジェクトのバックアップを行うことができる。

```bash:terminal
sudo gitlab-rake gitlab:backup:create
```

バックアップは、指定しないとすべてのオブジェクトが対象となるが、環境変数`SKIP`を利用することにより、状況に応じたバックアップをすることができる。  
`SKIP` で指定できるオプション一覧は以下のとおりである。

* `db` （データベース）
* `uploads` （添付ファイル）
* `repositories` （Git リポジトリデータ）
* `builds` （CI ビルドのログ）
* `artifacts` （CI のArtifacts）
* `lfs` （LFS オブジェクト）
* `registry` （コンテナレジストリ）
* `pages` （ユーザーカスタマイズページオブジェクト）


データベース、添付ファイルを除いたオブジェクトのバックアップを行うには、以下のコマンドを用いる。

```bash:terminal
sudo gitlab-rake gitlab:backup:create SKIP=db,uploads
```


また、基本的には/var/opt/gitlab/内にあるすべてのオブジェクトがバックアップされるが、環境変数の「SKIP」を利用することにより、ユーザーのニーズに応じて特定のデータだけをバックアップすることも可能です。

ここで作成されたバックアップアーカイブファイルは、バックアップ先として指定したディレクトリに以下のファイル名にて作成される。

* `EPOCH_YYYY_MM_DD_XX.XX.XX`という形式のタイムスタンプ  
  ただし、`XX.XX.XX`はGitLabのバージョンを表す。
* タイムスタンプ`EPOCH_YYYY_MM_DD_XX.XX.XX`を接頭辞とした``EPOCH_YYYY_MM_DD_XX.XX.XX_gitlab_backup.tar`というファイルが作成される。  

バックアップ先として指定したディレクトリのデフォルトでは`/var/opt/gitlab/backups`である。  
ディレクトリの指定先は、`/etc/gitlab/gitlab.rb` の以下のセクションにて定義を行う。

```
/etc/gitlab/gitlab.rb:# gitlab_rails[’backup_path’] = "/var/opt/gitlab/backups"
```


## リストア

一方、バックアップアーカイブファイルのリストアでは、生成したファイルのタイムスタンプを利用して行う。  
また、リストアを行う前にアプリケーションからの書き込み処理などの影響を受けないように、Unicorn とSidekiq を停止してからリストア作業を行う必要がある。

リストア対象のファイルを確認する。

```bash:terminal
ls /var/opt/gitlab/backups
```
```
1716293560_2024_05_21_17.0.0_gitlab_backup.tar
```

Unicorn およびSidekiq の停止を行い、確認する。

```bash:terminal
sudo gitlab-ctl stop unicorn
sudo gitlab-ctl stop sidekiq
sudo gitlab-ctl status
```

バックアップアーカイブファイルのリストアは以下の通り。
`BACKUP`には、リストア対象とするtarファイルのタイムスタンプ部分（`_gitlab_backup.tar`をのぞいた`EPOCH_YYYY_MM_DD_XX.XX.XX`）を指定する。

```bash:terminal
sudo gitlab-rake gitlab:backup:restore BACKUP=1716293560_2024_05_21_17.0.0
```

Unicorn およびSidekiq の起動と確認する。

```bash:terminal
sudo gitlab-ctl start
sudo gitlab-rake gitlab:check SANITIZE=true
```
