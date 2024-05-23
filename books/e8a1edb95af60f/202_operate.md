---
title: "基本的な運用"
---

## プロセス管理

GitLabのプロセス管理は、`gitlab-ctl`コマンドにより、集中的に行うことができる。

たとえば、各プロセスの起動や停止、およびステータス確認などを以下のコマンドで一度に行うことができる。

```bash:terminal
sudo gitlab-ctl status
```

```
run: alertmanager: (pid 15993) 234896s; run: log: (pid 15844) 234924s
run: gitaly: (pid 15955) 234897s; run: log: (pid 15250) 235037s
run: gitlab-exporter: (pid 15967) 234897s; run: log: (pid 15743) 234944s
run: gitlab-kas: (pid 126468) 78619s; run: log: (pid 15479) 235025s
run: gitlab-workhorse: (pid 15925) 234899s; run: log: (pid 15627) 234961s
run: logrotate: (pid 156396) 1049s; run: log: (pid 15203) 235048s
run: nginx: (pid 126479) 78619s; run: log: (pid 15651) 234956s
run: node-exporter: (pid 15950) 234898s; run: log: (pid 15691) 234950s
run: postgres-exporter: (pid 16003) 234895s; run: log: (pid 15874) 234918s
run: postgresql: (pid 15298) 235032s; run: log: (pid 15309) 235031s
run: prometheus: (pid 15977) 234896s; run: log: (pid 15817) 234930s
run: puma: (pid 126407) 78646s; run: log: (pid 15540) 234974s
run: redis: (pid 15206) 235045s; run: log: (pid 15225) 235042s
run: redis-exporter: (pid 15969) 234897s; run: log: (pid 15791) 234938s
run: sidekiq: (pid 126380) 78652s; run: log: (pid 15576) 234966s
```

`run:` はそのプロセスは起動されていること、`down:` は停止していることをそれぞれ示す。

GitLab全体の起動・停止・再起動はそれぞれ以下のように実行することができる。

```bash:terminal
sudo gitlab-ctl start     # GitLab全体の開始
sudo gitlab-ctl stop      # GitLab全体の停止
sudo gitlab-ctl restart   # GitLab全体の再起動
```

上記のコマンドの実行例では、すべてのプロセス状態が一度に処理されてしまいますが、それぞれのコマンドの後ろにプロセス名を入れることによって、プロセスごとに操作を行うことも可能である。

例えば、Nginxに限定してプロセス管理を行う場合は、以下の通りとなる。

```bash:terminal
sudo gitlab-ctl restart nginx  # Nginx の再起動
sudo gitlab-ctl status nginx   # Nginx のステータス確認
```

起動・停止だけでなくHUP シグナル（`SIGHUP`）やKILL シグナル（`SIGKILL`）をプロセスに送ることにより、プロセスのリロードや、どうしても停止できないプロセスの強制終了を行うことができる。

例えば、`SIGHUP` によるUnicorn の無停止リロードは以下の通り行う。

```bash:terminal
sudo gitlab-ctl hup unicorn
```

`SIGKILL` による、Nginx の強制停止と再起動は以下の通り行う。

```bash:terminal
sudo gitlab-ctl kill nginx
sudo gitlab-ctl restart nginx
```

## ログの確認

GitLab のログが`/var/log/gitlab/`以下に保存されている。  
各ミドルウェアごとの格納状況を一元的に確認することが、以下のコマンドにより可能である。

```bash:terminal
sudo gitlab-ctl tail
```

すべてのログがすぐに一覧できる一方で、標準出力はすべて出力されることから、エラーが起きているコンポーネントが特定できた場合は、そのコンポーネントに限定したログを追うようにすることを心掛けるべきである。


## バージョン確認

GitLabそのものや、GitLabにまつわるパッケージ一式のバージョンは以下のコマンドで確認できる。  
のちに述べるが、バックアップで取得したときのGitLabのバージョンとリストアで戻すときのGitLabのバージョンは一致していないといけない。
そのため、バックアップとして、バージョン情報も残しておく必要がある（ネーミングルールでわかるっちゃわかるけど、それはそれという感じで）。

```bash:terminal
sudo gitlab-rake gitlab:env:info
```

```
System information
System:         Ubuntu 22.04
Current User:   git
Using RVM:      no
Ruby Version:   3.1.5p253
Gem Version:    3.5.9
Bundler Version:2.5.9
Rake Version:   13.0.6
Redis Version:  7.0.15
Sidekiq Version:7.1.6
Go Version:     unknown

GitLab information
Version:        17.0.1
Revision:       bd824d1abb2
Directory:      /opt/gitlab/embedded/service/gitlab-rails
DB Adapter:     PostgreSQL
DB Version:     14.11
URL:            http://gitlab.local
HTTP Clone URL: http://gitlab.local/some-group/some-project.git
SSH Clone URL:  git@gitlab.local:some-group/some-project.git
Using LDAP:     no
Using Omniauth: yes
Omniauth Providers:

GitLab Shell
Version:        14.35.0
Repository storages:
- default:      unix:/var/opt/gitlab/gitaly/gitaly.socket
GitLab Shell path:              /opt/gitlab/embedded/service/gitlab-shell

Gitaly
- default Address:      unix:/var/opt/gitlab/gitaly/gitaly.socket
- default Version:      17.0.1
- default Git Version:  2.44.1.gl1
```
