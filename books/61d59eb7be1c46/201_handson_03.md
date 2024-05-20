---
title: "シナリオ3: 2つの機能の同時実装(プルリクエストあり)"
---

ここでは2つの機能を同時に実装し、一方の機能の実装途中ではあるものの、もう1つの機能の実装が求められた場合のフローについてハンズオンを行っていく。

## 1つ目の機能開発を行う

まずはブランチが`main`しかないことを確認する。

```bash:terminal
git branch
```

```
* main
```

ここで、先ほどと同様にブランチ`feature/my-feature-201`を作成する。

```bash:terminal
git switch -c feature/my-feature-201
git branch
```

```
* feature/my-feature-201
  main
```

ここで、成果物となるファイルを作成し、コミット・プッシュまでを行う。

```bash:terminal
echo "This is a new file." > new-file-201-01.txt
git add new-file-201-01.txt
git commit -m "Add new-file-201-01.txt"
git push -u origin feature/my-feature-201
```


## 2つ目の機能開発を行う




いったん`main`ブランチに戻し、`feature/my-feature-202`に移動する。

```bash:terminal
git switch main
git branch
```

```
  feature/my-feature-201
* main
```

```bash:terminal
git switch -c feature/my-feature-202
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
git branch
```

```
  feature/my-feature-201
  feature/my-feature-202
* main
```



```bash:terminal
git merge feature/my-feature-202
git push origin main
```

## 不要になった2つ目の機能開発用ブランチを削除する

```bash:terminal
git branch -d feature/my-feature-202
git branch
```

```
  feature/my-feature-201
* main
```

```bash:terminal
git push origin --delete feature/my-feature-202
```


## 最初の機能をさらに開発し、コミットする

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
git branch
```

```
  feature/my-feature-201
* main
```

```bash:terminal
git merge feature/my-feature-201
git push origin main
```

## 不要になった最初の機能開発用ブランチを削除する

```bash:terminal
git branch
```

```
  feature/my-feature-201
* main
```


```bash:terminal
git branch -d feature/my-feature-201
git branch
```

```
* main
```

```bash:terminal
git push origin --delete feature/my-feature-201
```

