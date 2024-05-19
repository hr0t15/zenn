---
title: "ケース2: 2機能の同時実装"
---

## 最初の機能開発用のブランチを作成する

```bash:terminal
git switch -c feature/my-feature-201
```

ブランチが2つあることを確認する。

```bash:terminal
git branch
```

```
* feature/my-feature-201
  main
```


ファイルを作成する。

```bash:terminal
echo "This is a new file." > new-file-201-01.txt
git add new-file-201-01.txt
git commit -m "Add new-file-201-01.txt"
```

プッシュする。

```bash:terminal
git push -u origin feature/my-feature-201
```


## 2つ目の機能開発用のブランチを作成する

```bash:terminal
git switch main
git switch -c feature/my-feature-202
```

ブランチが3つあることを確認する。

```bash:terminal
git branch
```

```
  feature/my-feature-201
* feature/my-feature-202
  main
```



## 2つ目の機能を開発し、コミット・プッシュする

```bash:terminal
echo "# Feature Branch Workflow" > new-file-202.txt
git add new-file-202.txt
git commit -m "Add new-file-202.txt"
git push -u origin feature/my-feature-202
```

## main ブランチにマージする

```bash:terminal
git switch main
git merge feature/my-feature-202
git push origin main
```

## 不要になった2つ目の機能開発用ブランチを削除する

```bash:terminal
git branch -d feature/my-feature-202
git push origin --delete feature/my-feature-202
```


```bash:terminal
git branch
```

```
  feature/my-feature-201
* main
```


## 最初の機能を開発し、コミットする

```bash:terminal
git switch feature/my-feature-201
git branch
```

```
* feature/my-feature-201
  main
```

```bash:terminal
echo "This is a new file." > new-file-201-02.txt
git add new-file-201-02.txt
git commit -m "Add new-file-201-02.txt"
git push -u origin feature/my-feature-201
```

## main ブランチにマージする

```bash:terminal
git switch main
git merge feature/my-feature-201
git push origin main
```

## 不要になった最初の機能開発用ブランチを削除する

```bash:terminal
git branch -d feature/my-feature-201
git push origin --delete feature/my-feature-201
```

```bash:terminal
git branch
```

```
* main
```
