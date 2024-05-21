---
title: "GitLabの構成"
---

Omnibus パッケージのセットアップが無事完了したので、それに伴うGitLab の運用管理およびそれに必要な内容について確認していく。

## GitLab上で稼働するミドルウェア

実際のGitLabでは、ミドルウェアは一通り選択可能であるが、Omnibus パッケージによるインストールを行うと、ミドルウェアは自動的に選択される。  
Omnibus パッケージにより導入される、主要なミドルウェアについてここでは紹介する。

* Nginx  
  Webサーバーおよびリバースプロキシ。静的コンテンツの提供や、リクエストをUnicornにプロキシするために使用される。
* Unicorn  
  GitLabのRailsアプリケーションを実行し、Nginxからのリクエストを処理する。
* PostgreSQL  
  データベース。GitLabのデータ（ユーザー情報、プロジェクト情報、イシュー、マージリクエストなど）を保存する。
* Sidekiq　 
  バックグラウンドジョブの処理。GitLabの非同期タスク（メール送信、リポジトリの更新、インデックス作成など）を処理を処理する。
* Redis  
  キーバリューストア。キャッシュ、セッション管理、Sidekiqのキュー管理などに使用される。


## ディレクトリ構造

Omnibus パッケージでは、自動的にディレクトリを配置を行ってくれる。
原則としては、一般的なLinuxのポリシーに準じての配置となるため、事前に確認しておく必要がある。

#### `/opt/gitlab/`

GitLab 実行ライブラリが全般的に格納されるディレクトリとなる。

#### `/var/opt/gitlab/`

GitLabのデータが全般的に格納されるディレクトリとなる。

* `git-data` リポジトリ配置ディレクトリ
* `git-data/repositories` Git リポジトリデータ
* `gitlab-rails/shared` オブジェクトの配置ディレクトリ
* `gitlab-rails/shared/artifacts` CI のArtifacts の配置
* `gitlab-rails/shared/lfs-objects` LFS オブジェクトの配置
* `gitlab-rails/uploads` ユーザー添付ファイルの配置
* `gitlab-rails/shared/pages` ユーザーカスタマイズページオブジェクト
* `gitlab-ci/builds` CI のビルドログ

#### `/etc/gitlab/` 

ユーザー設定ファイルが格納されるディレクトリとなる。  
特に`/etc/gitlab/gitlab.rb` は、設定ファイルである。


#### `/var/log/gitlab`

GitLab のログが全般的に格納されるディレクトリとなる。


## 管理コマンドの利用

GitLab上ではさまざまなミドルウェアが稼働しているが、Omnibus パッケージには統一的な管理を実現するための管理コマンドが存在する。  
ここでは、その管理コマンドについて確認する。

GitLab の運用でよく利用されるものは以下の3 つのコマンドである。

* gitlab-ctl
* gitlab-psql
* gitlab-rake


#### gitlab-ctl

管理コマンドの中でも頻繁に利用するコマンドであり、おもに以下の処理が可能である。

* GitLab上で動作するプロセスの管理
* 設定の再構成
* ログの確認

特にOmnibus パッケージでは、GitLab の変更操作は全てこの`gitlab-ctl` コマンドから行うことが原則となっている。

#### gitlab-psql

Omnibus パッケージでインストールを行ったときにインストールされるGitLab 専用のPostgreSQL との接続に利用する。  

Omnibus パッケージでは、適正値に設定済みのGitLab 専用データベース（gitlabhq_production）となっていることから、障害時におけるテーブルの確認など、参照のみに絞った利用が推奨される。

#### gitlab-rake

GitLab における、頻繁に利用される運用タスクが定義されている。  
Rakeという、Rubyのビルドツールにより実装されているものである。

gitlab-rake を利用する際は、[Rake tasks - 公式ドキュメント](https://docs.gitlab.com/ee/raketasks/)も併せて確認してほしい。
