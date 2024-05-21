---
title: "GitLab のSSL/TLS 対応"
---


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

