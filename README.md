# Zenn CLI + alpha

## Zenn CLIまわりのドキュメント

以下の記事を読んでおけば、サイクルは一通り理解可能。

* [Zenn CLIで記事・本を管理する方法](https://zenn.dev/zenn/articles/zenn-cli-guide)
* [GitHubリポジトリでZennのコンテンツを管理する](https://zenn.dev/zenn/articles/zenn-cli-guide)

## TOPIC

### ZennのMarkdown記法一覧

[ZennのMarkdown記法一覧](https://zenn.dev/zenn/articles/markdown-guide) を見よう。

### ブラウザでのプレビュー

本文の執筆は、ブラウザでプレビューしながら確認できる。  
ブラウザでプレビューするためには次のコマンドを実行します。

```bash
npx zenn preview
```

デフォルトでは`localhost:8000`で立ち上がりますが、以下のようにポート番号の指定もできる。

```bash
npx zenn preview --port 3000
```

バックグラウンド実行は以下にてできる。

```bash
nohup npx zenn preview --port 8000 &
```

* `&` を使うと、コマンドがバックグラウンドで実行される。
* `nohup` コマンドを使うと、ターミナルを閉じてもプロセスが継続して実行される。


## Zenn CLIのインストール

[Zenn CLIをインストールする](https://zenn.dev/zenn/articles/install-zenn-cli)をベースに記載する。


### node.jsのインストール

[Download Node.js](https://nodejs.org/en/download/package-manager)にて使用のアーキテクチャなどを指定すると、インストール方法が出力される。  
それをベースに導入を行う。

Ubuntu Serverへの導入は事前にcurlとunzipがインストールされている必要があるため、事前にインストールを行う。

```bash
sudo apt install -y curl unzip
```

上記のインストールが終わったら、fnmをインストールする。

```bash
# installs fnm (Fast Node Manager)
curl -fsSL https://fnm.vercel.app/install | bash
```

`~/.bashrc`の再読み込みを行い、nodejsをインストールする。

```bash
source ~/.bashrc
fnm use --install-if-missing 20
```

nodeとnpmのバージョンを確認し、これらがインストールできたことを確認する。

```bash
node -v
npm -v
```

### CLIのインストール

Zennのコンテンツを管理したいディレクトリで、以下のコマンドを実行する。

```bash
# プロジェクトをデフォルト設定で初期化
npm init --yes
# zenn-cliを導入
npm install zenn-cli
```

これでディレクトリにCLIがインストールされる。

Zenn用のセットアップを行うため、以下のnpxコマンドを実行します。

```bash
npx zenn init
```

README.mdや.gitignoreのほか、articlesとbooksという名前のディレクトリが作成されます。この中にmarkdownファイル（◯◯.md）を入れていくことになります。

これでZenn CLIの導入は完了です🎉

### CLIのアップデート

Zenn CLIの表示がzenn.devと異なるときやCLI利用時に更新通知が表示されたときは下記のコマンドでアップデートを行ってください。

```bash
npm install zenn-cli@latest
```

