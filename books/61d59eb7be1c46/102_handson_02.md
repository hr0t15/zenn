---
title: "シナリオ2: 1つの機能の実装（プルリクエストあり）"
---

## 1. フィーチャーブランチの作成

新しい機能を開発するために、フィーチャーブランチ`feature/my-feature-101`を作成し、切り替えも同時に行う。

```bash:terminal
git switch -c feature/my-feature-101
```

`git switch`コマンドは、ブランチの切り替えを行うコマンドだが、`-c`を付与することで、新規作成したうえで切り替えも同時に行ってくれる。


`git branch`コマンドは、現在のリポジトリのブランチを管理するために使用される。  
このコマンドを実行すると、ローカルリポジトリ内のすべてのブランチが表示され、現在チェックアウトされているブランチがアスタリスク（`*`）で示される。

現時点でリポジトリが2つあり、チェックアウトされているブランチは`feature/my-feature-101`であることを確認する。

```bash:terminal
git branch
```

```
* feature/my-feature-101
  main
```


## 2. 機能の開発

フィーチャーブランチでコードを編集し、新しい機能を開発する。

```bash:terminal
echo "This is a new file." > new-file-101.txt
```

開発に伴うコミットを行う。

```bash:terminal
git add new-file-101.txt
git commit -m "Add new-file-101.txt"
```

開発中は適宜コミットを行うことを推奨する。  
なお、プッシュも適宜行うことを推奨する。

```bash:terminal
git push -u origin feature/my-feature-101
```

## 3. ブランチのマージ

該当機能の開発が完了したら、ブランチのマージを行う。

まずはmainリポジトリをチェックアウトする。

```bash:terminal
git switch main
git branch
```

```
  feature/my-feature-101
* main
```

`git merge`コマンドにより、指定したブランチを現在チェックアウトされているブランチにマージする。  
以下により、現在チェックアウトされている`main`に、`feature/my-feature-101`をマージしている。

```bash:terminal
git merge feature/my-feature-101
```

なお、マージによるコンフリクトが発生した場合、手動で解決する必要がある。

最後に`main`ブランチの最新の変更をリモートリポジトリにプッシュする。

```bash:terminal
git push origin main
```

## 4. 不要ブランチの削除

マージが完了したら、不要になったブランチを削除する。  
これはローカルリポジトリ・リモートリポジトリに対して実施する必要がある。

まずは、ローカルリポジトリの削除を行う。  
`git branch`コマンドの`-d`を用いることで、ローカルリポジトリから指定されたブランチを削除することができる。  
なお、この操作は、ブランチが他のブランチにマージされていない変更を持たない場合にのみ成功し、マージされていない変更がある場合、削除は失敗する。

まずは現状のブランチの状況を確認する。

```bash:terminal
git branch
```

```
  feature/my-feature-101
* main
```

以下により、ローカルリポジトリから`feature/my-feature-101`ブランチを削除する。

```bash:terminal
git branch -d feature/my-feature-101
git branch
```

```
* main
```

ここで、削除しようとしているブランチに未マージのコミットがある場合、エラーが発生し、削除は拒否される。  
強制的に削除する場合は`-D`オプションを使用する。

次に、リモートリポジトリの削除を行う。
`git push`コマンドの`--delete`を用いることで、リモートリポジトリから指定されたブランチを削除することができる。  

以下により、リモートリポジトリから`feature/my-feature-101`ブランチを削除する。

```bash:terminal
git push origin --delete feature/my-feature-101
```

## 次の機能開発を行う

手順1.から4.を繰り返す。
