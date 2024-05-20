---
title: "Let's Encryptの証明書を取得する"
emoji: "🌊"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["openssl"]
published: false
---


手順は以下の通りです。


（必要な場合）ファイアウォールの設定

証明書を取得するためには、ポート80（または443）が開いている必要があります。  
UFWを使用している場合、以下のコマンドでポートを開けます。

```bash:terminal
sudo ufw allow 80/tcp
sudo ufw allow 443/tcp
sudo ufw reload
```

Certbot をインストールします。

```bash:terminal
sudo apt update
sudo apt install certbot python3-certbot-nginx
```

standalone モードで Certbot を実行し、証明書を取得します。


```bash:terminal
sudo certbot certonly --standalone -d example.com -d www.example.com
```

example.com と www.example.com を実際のドメイン名に置き換えてください。

メールアドレスを入力し、利用規約に同意します。  
Certbot が一時的に Web サーバーを起動し、Let's Encrypt サーバーが検証を行います。

証明書が発行されると、以下のディレクトリに保存されます。

* `/etc/letsencrypt/live/example.com/`


証明書を使用するには、Web サーバーや他のサービスの設定ファイルで、以下のファイルを指定します。

* 証明書: `/etc/letsencrypt/live/example.com/fullchain.pem`
* 秘密鍵: `/etc/letsencrypt/live/example.com/privkey.pem`


証明書の自動更新設定
Certbotは自動で証明書を更新する機能を持っています。以下のコマンドで自動更新が正しく動作するかテストできます。

```bash:terminal
sudo certbot renew --dry-run
```


## （オプション）自動更新用のスクリプト

自動更新が失敗した場合に備えて、更新後にサービスを再起動するスクリプトを作成することもできます。  
例えば、renew_certificates.shというスクリプトを作成して、更新後に必要なサービスを再起動します。

```bash:terminal
sudo nano /usr/local/bin/renew_certificates.sh
```

スクリプト内容（例）：

```bash:terminal
#!/bin/bash

certbot renew --quiet --renew-hook "systemctl restart your-service"
```

スクリプトに実行権限を与えます。

```bash:terminal
sudo chmod +x /usr/local/bin/renew_certificates.sh
```

このスクリプトをcronジョブに設定して定期的に実行します。

```bash:terminal
sudo crontab -e
```
以下の行を追加して、毎日午前3時にスクリプトを実行します。

```bash:terminal
0 3 * * * /usr/local/bin/renew_certificates.sh
```


standalone モードを使用する場合、証明書を取得する際に一時的に Web サーバーを起動するため、通常の Web サーバーが使用するポート（80 と 443）を開放する必要があります。  
また、証明書の取得やルート証明書の変更の際は、サービスの再起動が必要になる場合があります。

