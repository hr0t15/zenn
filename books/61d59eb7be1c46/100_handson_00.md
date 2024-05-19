---
title: "事前準備"
---


## リモートリポジトリの作成

リモートリポジトリとは、GitHubやGitLabに作成されたリポジトリのことを指す。  
リモートリポジトリとして、GitHubやGitLab上に新しいリポジトリを作成する。

* リポジトリ名: `branch-workflow-handson`

## ローカル端末の初期設定

Gitをインストールし、リモートリポジトリに対してのssh接続が可能となるように設定をする。  
ここで、Gitのバージョンは2.23.0以降とする（`git switch`コマンドを用いるため）。

そのうえで、先ほど作成したリポジトリ `branch-workflow-handson` をクローンする。

```bash:terminal
GITHUB_USERNAME=hr0t15
git clone https://github.com/${GITHUB_USERNAME}/branch-workflow-handson.git
cd branch-workflow-handson
```

README.md を作成し、コミットまで行う。

```bash:terminal
echo "# branch workflow handson" > README.md
git add README.md
git commit -m "first commit"
```

現在のブランチ名を`main`に変更する。  
ここでは、`-M`により、名前変更を強制的に実行する。

```bash:terminal
git branch -M main
```

`git push`によりプッシュする。  
各オプションは以下の通りの意味である。

* `-u`：`--set-upstream`の短縮形で、指定されたリモートブランチを追跡する設定を行う。  
  これにより、以降の`git pull`や`git push`コマンドで、ブランチを明示的に指定しなくても自動的にこのリモートブランチを使用する。
* `origin`：リモートリポジトリの名前。通常、リモートリポジトリのデフォルト名は`origin`である。
* `main`：プッシュするローカルブランチの名前。

```bash:terminal
git push -u origin main
```

