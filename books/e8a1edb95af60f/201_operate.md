---
title: "基本的な運用"
---

## GitLabを支えるミドルウェア

GitLabで利用される主要なミドルウェアは以下の5つです。

Nginx:

役割: Webサーバーおよびリバースプロキシ。静的コンテンツの提供や、リクエストをUnicornにプロキシするために使用されます。
Unicorn:

役割: Ruby on Railsアプリケーションサーバー。GitLabのWebアプリケーション部分を処理します。
PostgreSQL:

役割: データベース。GitLabの主要なデータを保存するために使用されます。
Sidekiq:

役割: 背景ジョブの処理。メールの送信、リポジトリの更新、CI/CDパイプラインの実行など、バックグラウンドタスクを処理します。
Redis:

役割: キーバリューストア。キャッシュやセッションストレージ、ジョブキューとして使用されます。
これらのミドルウェアは、GitLabのパフォーマンスとスケーラビリティを向上させるために重要な役割を果たしています。




itLabで主に利用される主要ミドルウェアは以下の5つです。

Nginx

Webサーバーとしての役割を担います。
リバースプロキシとして機能し、外部からのHTTPリクエストを受け取り、適切なバックエンドサーバー（Unicornなど）に転送します。


Unicorn

Rubyアプリケーションサーバーです。
GitLabのRailsアプリケーションを実行し、Nginxからのリクエストを処理します。


PostgreSQL

リレーショナルデータベースシステムです。
GitLabのデータ（ユーザー情報、プロジェクト情報、イシュー、マージリクエストなど）を保存します。


Sidekiq

Rubyのバックグラウンドジョブ処理システムです。
GitLabの非同期タスク（メール送信、リポジトリの更新、インデックス作成など）を処理します。


Redis

インメモリのキーバリューストアです。
GitLabでは、キャッシュ、セッション管理、Sidekiqのキュー管理などに使用されます。



これらのミドルウェアは、GitLabの主要コンポーネントとして機能し、GitLabの全体的なアーキテクチャを支えています。各ミドルウェアは役割を分担し、それぞれの得意分野で機能することで、GitLabの高い拡張性、パフォーマンス、および信頼性を実現しています。




## 運用について


Omnibus パッケージのセットアップが無事完了したら、GitLab の運用管理方法を確認しておきましょう。
特に、GitLab の起動・停止や設定変更などの管理コマンドの使い方は、定常業務においてもよく実施される基本の操作です。
また、運用を行ううえでは、Omnibus パッケージでインストールされる各アプリケーションの設定ファイルが、どこに配置されるかを把握しておく必要があります。

ここでは、まずGitLab の基本運用操作ができるように、管理コマンドや各コンポーネントのディレクトリ構成を紹介します。

## ディレクトリ構造と権限

Omnibus パッケージでは、GitLab に必要なユーザーを作成するとともに、各コンポーネントのディレクトリとその所有権を自動で設定します。あらかじめログや設定ファイルの配置場所を確認しておくことにより、設定変更やトラブルシューティングなどを円滑に行うことができます。

### 管理ユーザーとグループ

Omnibus パッケージでは、システムユーザーとGitLab の各コンポーネントを起動するユーザーが明確に分離されています。  
GitLab においては、リポジトリなどのコンテンツの管理やデータベースにあるデータ管理など、コンポーネントごとに多くのデータを取り扱うため、互いに他のデータに影響を及ぼさないように管理ユーザーを分離する仕組みがあります。

* `git` GitLab のデフォルト管理ユーザー
* `gitlab-www` Nginx などのWeb プロセスを稼働している管理ユーザー
* `gitlab-redis` Redis のコンテンツやサービスを稼働している管理ユーザー
* `gitlab-psql` PostgreSQL のデータやサービスを稼働している管理ユーザー
* `gitlab-prometheus` Prometheus の監視データやサービスを稼働している管理ユーザー

デフォルトの設定では、主にこれらのユーザー、およびそれと同一のグループ名が作成されます。
ただし、あらかじめGit をインストールしてあり、既存のgit ユーザーが存在する場合は、そのユーザーが利用されます。

### ディレクトリ構造

Omnibus パッケージでは、決められたユーザー権限で自動的にディレクトリを配置してくれます。
ただし、設定ファイルおよびコンテンツやログなど、それぞれの用途に応じて配置場所が異なっているため、事前に確認しておきましょう。

Table 2-3 Omnibus パッケージの主なディレクトリ
|ディレクトリ|サブディレクトリ|Permission |権限|用途|
|- |- |- |- |- |
|`/opt/gitlab/` ||||GitLab 実行ライブラリのディレクトリ|
||`bin` |0755 |root:root| GitLab 管理コマンドの配置|
||`etc` |0755 |root:root| 実行環境変数や設定の配置|
||`embedded` |0755 |root:root| 各プロセスのバイナリやファイルの配置|
||`sv`| 0755| root:root| runsvdir| プログラムの配置|
|`/var/opt/gitlab/` ||||GitLab データディレクトリ|
||`git-data` |0700 |git:root |リポジトリ配置ディレクトリ|
||`git-data/repositories` |2770 |git:root |Git リポジトリデータ|
||`gitlab-rails/shared` |0751 |git:gitlab-www |オブジェクトの配置ディレクトリ|
||`gitlab-rails/shared/artifacts` |0700 |git:root |CI のArtifacts の配置|
||`gitlab-rails/shared/lfs-objects` |0700 |git:root |LFS オブジェクトの配置|
||`gitlab-rails/uploads` |0700 |git:root |ユーザー添付ファイルの配置|
||`gitlab-rails/shared/pages` |0750 |git:gitlab-www |ユーザーカスタマイズページオブジェクト|
||`gitlab-ci/builds` |0700 |git:root |CI のビルドログ|
|`/etc/gitlab/` ||||GitLab ユーザー設定ファイルディレクトリ
||`gitlab.rb` |0600 |root:root |設定ファイル|
|`/var/log/gitlab` ||||GitLab ログディレクトリ|

運用者がよく利用するファイルは「`/etc/gitlab/gitlab.rb`」です。
ここで定義したプロセスの設定が`/etc/gitlab` 配下に配置されます。また「`/var/opt/gitlab`」配下はデータが保管されるディレクトリとなっているため、NFS などの共有ディレクトリで別途管理することをおすすめします。
ただしNFS マウントを利用した場合は「`root_squash`」オプションを有効化し、root ユーザーによるディレクトリ生成を許可しないよう注意が必要です。


## 管理コマンドの利用

今度は管理コマンドの利用について説明します。GitLab をインストールすると多くのプロセスがサーバー上に導入されるため、一つひとつのプロセスを管理していては運用負荷が増大してしまいます。
そのようなときに役立つのがOmnibus パッケージに備わっている管理コマンドです。
GitLab の運用でよく利用されるものは以下の3 つのコマンドです。

* gitlab-ctl
* gitlab-psql
* gitlab-rake

これらは個々のプロセス管理を包括的に操作できるように作成されている、いわゆるラッパーコマンドです。
Omnibus パッケージでインストールした場合は、専用コマンドを利用することによって、各プロセスの管理コマンドを利用せずとも、GitLab のオペレーションを簡易に実施することができます。

### gitlab-ctl

管理コマンドの中でも頻繁に利用するのが、このgitlab-ctl コマンドです。
このコマンドはGitLab上で動作するプロセスの「プロセス管理」や「設定の再構成」、また「ログの確認」を一元的に操作できるため、GitLab の運用には欠かせないコマンドの一つになっています。
特にOmnibus パッケージでは、GitLab の変更操作は全てこのgitlab-ctl コマンドから行うことが原則です。
これは運用もすべて構成ファイルに統一することによって品質を担保する仕組みです。

通常、GitLab の中にあるプロセスを停止しようとすると、Unicorn 上のGitLab Rails、Nginx、PostgreSQLなど、それぞれのプロセスを正しい順番で停止する必要がありますが、gitlab-ctl を利用すれば、すべてのプロセスを順序通りに起動・停止することができます。
これはプロセスの自動起動などに利用されているRunit サービスディレクトリ（runsvdir）の機能を利用しており、GitLab で動作するプロセスの状態をrunsvdir プロセス経由で操作する仕組みとなっています。

#### プロセス管理

GitLab のプロセス管理はとても簡単で、以下のコマンドで各プロセスの起動や停止、およびステータス確認などを一度に行うことができます。

gitlab-ctl によるプロセス管理
```
# GitLab の各プロセスのステータス確認
$ sudo gitlab-ctl status
run: gitaly: (pid 4683) 917319s; run: log: (pid 4115) 917401s
run: gitlab-monitor: (pid 4739) 917317s; run: log: (pid 4574) 917352s
run: gitlab-workhorse: (pid 4696) 917318s; run: log: (pid 4165) 917395s
run: logrotate: (pid 23501) 2039s; run: log: (pid 4267) 917383s
run: nginx: (pid 4227) 917389s; run: log: (pid 4226) 917389s
run: node-exporter: (pid 4400) 917371s; run: log: (pid 4399) 917371s
run: postgres-exporter: (pid 4725) 917317s; run: log: (pid 4515) 917358s
run: postgresql: (pid 3832) 917494s; run: log: (pid 3831) 917494s
run: prometheus: (pid 7382) 915083s; run: log: (pid 4335) 917377s
run: redis: (pid 3721) 917500s; run: log: (pid 3720) 917500s
run: redis-exporter: (pid 4470) 917365s; run: log: (pid 4469) 917365s
run: sidekiq: (pid 4070) 917407s; run: log: (pid 4069) 917407s
run: unicorn: (pid 4015) 917413s; run: log: (pid 4014) 917413s
# GitLab の起動
$ sudo gitlab-ctl start
# GitLab の停止
$ sudo gitlab-ctl stop
# GitLab の再起動
$ sudo gitlab-ctl restart
```

ステータスを確認した際に「run:」と表示されるとそのプロセスは起動されていることを示し、「down:」では停止していることを示します。
ただし、上記のコマンドの実行例では、すべてのプロセス状態が一度に処理されてしまいますが、それぞれのコマンドの後ろにプロセス名を入れることによって、プロセスごとに操作を行うことも可能です。

gitlab-ctl による各プロセス管理
```
# Nginx の再起動
$ sudo gitlab-ctl restart nginx
ok: run: nginx: (pid 4924) 1s
# Nginx のステータス確認
$ sudo gitlab-ctl status nginx
run: nginx: (pid 4819 ） 29s; run: log: (pid 3893) 419s
```

また、起動・停止だけでなくHUP シグナル（SIGHUP）やKILL シグナル（SIGKILL）といったようなシグナルを各プロセスに送ることにより、プロセスのリロードや、どうしても停止できないプロセスの強制終了が行えます。

gitlab-ctl によるシグナル送信
```
# SIGHUP による、Unicorn の無停止リロード
$ sudo gitlab-ctl hup unicorn
# SIGKILL による、Sidekiq の強制停止（※強制停止後は再起動を行ってください）
$ sudo gitlab-ctl kill sidekiq
$ sudo gitlab-ctl restart sidekiq
ok: run: sidekiq: (pid 6374) 0s
```

プロセスに対するシグナルの送信は運用で必要な際に一時的に利用するオペレーションであり、定常運用ではstart / stop などのオプションを利用することをおすすめします。


#### 設定の再構成

各コンポーネントの設定変更や起動ポートの変更など、Omnibus パッケージのGitLab 設定はすべて「`/etc/gitlab/gitlab.rb`」に定義します∗5。

*5 Configuration Option [https://gitlab.com/gitlab-org/omnibus-gitlab/blob/master/doc/settings/configuration.md](https://gitlab.com/gitlab-org/omnibus-gitlab/blob/master/doc/settings/configuration.md)

これはコードですべての運用を行うというInfrastructure as Code の概念で構成されており、各コンポーネントの設定ファイルなどを個別に修正することはありません。
どの設定であっても基本gitlab.rb を修正し、再構成コマンドを実行することによって運用を行います。
また、運用中の再構成に関しては、GitLab のサービス全体に影響を及ぼすため、メンテナンス状態での反映をおすすめします。

gitlab-ctl による設定反映
```
$ sudo gitlab-ctl reconfigure
```

#### ログの確認

GitLab では、ログが「/var/log/gitlab/」以下に保存されますが、これらのログはプロセスごとに分類されているため確認するのがとても大変です。
その煩わしさを一元的に管理できるのが、gitlab-ctlのtail オプションです。
これを利用することにより、全プロセスの最新ログを標準出力上で監視することができます。

gitlab-ctl によるログの確認
```
$ sudo gitlab-ctl tail
==> /var/log/gitlab/gitlab-monitor/current <==
2017-05-20_06:12:43.94392 127.0.0.1 - - [20/May/2017:15:12:43 JST] "GET /proces
s HTTP/1.1" 200 1529
2017-05-20_06:12:43.94438 - -> /process
…
```

すべてのログがすぐに一覧できるのでとても便利なオプションですが、トラブルシューティングなどの際は標準出力が多発してしまうため、最終的にはエラーを起こしているコンポーネントを特定し、ログディレクトリ上のログを追うようにしてください。

### gitlab-psql

Omnibus パッケージでインストールを行うとGitLab 専用のPostgreSQL がインストールされますが、その接続にはgitlab-psql コマンドを利用します。
このコマンドはパッケージに含まれるpsql コマンドをラッピングしたコマンドとなっており、PostgreSQL に作成されたGitLab データベースの閲覧や変更を行うことができます。
ただし、Omnibus パッケージでは、適正値に設定済みのGitLab 専用データベース（gitlabhq_production）となっているため、更新処理の利用ではなく、障害時におけるテーブルの確認など、あくまで参照用としての利用を推奨します。

gitlab-psql によるPostgreSQL の接続
```
$ sudo gitlab-psql -d gitlabhq_production
gitlabhq_production=# \l ## データベースの一覧表示
gitlabhq_production=# \d ## gitlabhq_production データベースのテーブル一覧表示
gitlabhq_production=# \d users ##users テーブルの項目（フィールド）表示
gitlabhq_production=# \q
```

ログインに成功するとプロンプトが「gitlabhq_production=#」となります。
gitlab-psql は、Omnibus パッケージでローカルにインストールしたPostgreSQL にのみ対応しており、リモートホストやソースコードからのインストールでは利用できません。
また、このコマンドはGitLab インストール後の再構成（gitlab-ctl reconfigure）を一度行った後から利用することができます。


#### アクセス権限エラーの対応

もし、特定ユーザーしかアクセス権限がないディレクトリ上で、gitlab-psql コマンドを利用すると、以下のような警告が出力されます。
ただし、gitlab-psql の操作内容に影響はないので、そのまま無視していただいて構いません。
もしくは/tmp などに移動したうえで、gitlab-psql を実行してみてください。

gitlab-psql の接続警告
```
could not change directory to "/root"
```

### gitlab-rake

通常、Rake はRuby で実装されたビルドツールですが、GitLab では頻繁に利用される運用タスク（Rakefile）がすでにいくつか定義されており、それをgitlab-rake で実行することが可能です。
ここではGitLab のバックアップとリストアのタスクに関してのみ紹介しますが、その他にもさまざまなタスクが用意されています。
Table 2-4 はその一例です。

Table 2-4 Rake tasks のオプション例
|コマンドオプション|詳細|
|- |- |
|gitlab-rake cache:clear:db |データベースのキャッシュ削除|
|gitlab-rake cache:clear:redis |Redis のキャッシュ削除|
|gitlab-rake gitlab:app:check |GitLab Rails の設定チェック|
|gitlab-rake gitlab:backup:create |GitLab オブジェクトのバックアップ|
|gitlab-rake gitlab:backup:restore |GitLab オブジェクトのリストア|
|gitlab-rake gitlab:check |GitLab の設定チェック|
|gitlab-rake gitlab:cleanup:repos |Rename 前などの古いリポジトリの削除|
|gitlab-rake gitlab:env:info |GitLab の環境詳細情報|
|gitlab-rake gitlab:repo:check |GitLab 環境下のリポジトリチェック|
|gitlab-rake gitlab:user:check_repos |特定ユーザーのリポジトリチェック|
|gitlab-rake import:github GitHub |プロジェクトのインポート|

gitlab-rake を利用する際は、公式のドキュメントも併せて確認を行ってください。

* https://docs.gitlab.com/ce/raketasks/README.html

#### バックアップの取得、リストア

gitlab-rake によるアプリケーションデータのバックアップでは、データベースやGit リポジトリ、およびすべての添付ファイルをまとめたバックアップアーカイブファイルが作成されます。
作成されたバックアップアーカイブファイルは、同じバージョンのGitLab にのみ展開することができます。
また、基本的には/var/opt/gitlab/内にあるすべてのオブジェクトがバックアップされますが、環境変数の「SKIP」を利用することにより、ユーザーのニーズに応じて特定のデータだけをバックアップすることも可能です。

SKIP で指定できるオプション一覧

* db （データベース）
* uploads （添付ファイル）
* repositories （Git リポジトリデータ）
* builds （CI ビルドのログ）
* artifacts （CI のArtifacts）
* lfs （LFS オブジェクト）
* registry （コンテナレジストリ）
* pages （ユーザーカスタマイズページオブジェクト）

gitlab-rake によるバックアップの取得

```
## 全オブジェクトのバックアップ
$ sudo gitlab-rake gitlab:backup:create
## データベース、添付ファイルを除いたオブジェクトのバックアップ
$ sudo gitlab-rake gitlab:backup:create SKIP=db,uploads
```

ここで作成されたバックアップアーカイブファイルは、「EPOCH_YYYY_MM_DD_GitLab バージョン」というタイムスタンプの後に、ファイル名で「[TIMESTAMP]_gitlab_backup.tar」というファイルが、/var/opt/gitlab/backups 配下に生成されます。

ここで指定されているバックアップ場所は、「/etc/gitlab/gitlab.rb」内で定義します。

バックアップの取得ディレクトリ
```
/etc/gitlab/gitlab.rb:# gitlab_rails[’backup_path’] = "/var/opt/gitlab/backups"
```

一方、バックアップアーカイブファイルのリストアでは、生成したファイルのタイムスタンプを利用して行います。
また、リストアを行う前にアプリケーションからの書き込み処理などの影響を受けないように、Unicorn およびSidekiq を停止してからリストア作業を行ってください。

バックアップアーカイブファイルのリストア
```
$ ls /var/opt/gitlab/backups
1514961545_2018_12_21_10.2.5_gitlab_backup.tar
## Unicorn およびSidekiq の停止
$ sudo gitlab-ctl stop unicorn
$ sudo gitlab-ctl stop sidekiq
$ sudo gitlab-ctl status
## バックアップアーカイブファイルのリストア
$ sudo gitlab-rake gitlab:backup:restore BACKUP=1514961545_2018_12_21_10.2.5
## Unicorn およびSidekiq の起動と確認
$ sudo gitlab-ctl start
$ sudo gitlab-rake gitlab:check SANITIZE=true
```

プロジェクト単位のリストア方法ができたため、このように、gitlab-rake を利用すると、複雑な運用タスクも簡単に処理することができるため、独自運用スクリプトを用意する前にgitlab-rake でできる作業を確認しておきましょう。



## GitLab のSSL/TLS 対応

本書では、HTTP 通信によるGitLab のインストール手順を紹介しましたが、ログイン情報や機密情報を取り扱うリポジトリではSSL/TLS による通信の暗号化が重要です。
GitLab のHTTPS を有効化するためには、Omnibus パッケージをインストールした後に以下の作業を行ってみましょう。

### (1)SSL/TLS 証明書の配備

まずは信頼されるルート認証局から取得したSSL/TLS 証明書を用意します。
また、時間やコストを抑えてSSL/TLS 証明書を発行したい場合は、各クラウドベンダーが提供するSSL/TLS 作成サービスや、Let's Encrypt（https://letsencrypt.jp/）を利用することで、素早くSSL/TLS 証明書を用意できます。

Omnibus パッケージのデフォルト設定では、「/etc/gitlab/ssl」以下にドメイン名の公開鍵（FQDN.crt）、および秘密鍵（FQDN.key）を配備することで、Nginx がそれらを参照します。

```
$ sudo mkdir -p /etc/gitlab/ssl
$ sudo chmod 700 /etc/gitlab/ssl
$ sudo cp gitlab.example.com.key gitlab.example.com.crt /etc/gitlab/ssl/
```

ただし、秘密鍵がパスワードで保護されている場合は、事前にパスワードを削除して展開しなければいけません。

```
$ sudo /opt/gitlab/embedded/bin/openssl rsa -in gitlab.example.com_before.key \
-out gitlab.example.com_after.key
```

### (2)GitLab の再構成

後は通常の構成変更同様に、gitlab.rb を変更してから再構成を行います。基本、変更する内容は、external_url のみです。
ただし、公開鍵と秘密鍵の場所を変更する際は、ssl_certificate(_key）を指定してください。

```
$ sudo vi /etc/gitlab/gitlab.rb
### HTTPS 設定
external_url ’https://gitlab.example.com’
### HTTP アクセスをHTTPS にリダイレクト
nginx[’redirect_http_to_https’] = true
### 公開鍵、秘密鍵の場所指定
nginx[’ssl_certificate’] = "/etc/gitlab/ssl/#{node[’fqdn’]}.crt"
nginx[’ssl_certificate_key’] = "/etc/gitlab/ssl/#{node[’fqdn’]}.key"
$ sudo gitlab-ctl reconfigure
```

これらの設定により、ユーザーへの通信が保護され、パスワードやコードが外部から読み取られたり改ざんされないように保護できます。

