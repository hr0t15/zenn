---
title: "GitLabにおける設定変更"
---

各コンポーネントの設定変更や起動ポートの変更など、Omnibus パッケージのGitLab 設定はすべて`/etc/gitlab/gitlab.rb`にて定義を行う。

ミドルウェアごとに設定ファイルを指定するのではなく、統一的にgitlab.rb を修正し、再構成コマンドを実行することによって運用を行うこととなる。
定義内容については、[Configuration Option](https://gitlab.com/gitlab-org/omnibus-gitlab/blob/master/doc/settings/configuration.md)を参照されたい。


再構成は以下のコマンドにより行う。

```
$ sudo gitlab-ctl reconfigure
```

なお、運用中の再構成に関しては、GitLab のサービス全体に影響を及ぼすことから、メンテナンス状態での反映をおすすめする。  
以降は、設定変更に関して、トピック単位で解説を行う。
