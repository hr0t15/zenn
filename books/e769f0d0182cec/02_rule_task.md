---
title: "ルールとタスク"
---

# ルール

## 基本的なフォーマット

Makefile には処理をさせたいルールを記述していきます。  
ルールは次のようなフォーマットになります。

```Makefile
<target> [<target>...]: [<dependency>...]
    <command>
    <command>
```

このひとかたまりをルールと呼びます．

特にルールの中にある`<target>`のことを**ターゲット**と呼びます．  
通常、ターゲットには、生成したいファイルを指定します。

`<dependency>`は、前提条件としてターゲットが依存するファイルを指定します。

`<command>`にはターゲットで指定したファイルを生成するためのコマンドを記述します。
`<command>`の手前には，タブ1文字を入れることに注意してください．  
もし、`Makefile:xx: *** missing separator. Stop.` といったエラーが発生した場合、タブ文字 になってない可能性があります。  
タブ文字 になっているか確認してください。

例えば，"Hello World!"という文字が入ったhello.txtというファイルを作るMakefileは次のようになります．

```Makefile:Makefile
hello:
	echo Hello World
```

```bash:Terminal
make hello
```

```
echo Hello World
Hello World
```

## `@`による非表示化

先頭に `@` をつけると、make実行時にコマンドが表示されなくなります。

```Makefile:Makefile
hello:
	@echo Hello World
```

```bash:Terminal
make hello
```

```
Hello World
```

## 複数行の記述

また，[そのファイルを作るためのコマンド行] は複数行書きこんでもかまいません．  
hello.txt というファイルを，HelloとWorldの2行からなるテキストファイルとして作成する場合は次のようにします．  
このとき，2行目以降にも行頭にタブ1文字いれることに注意してください．  
makeは最初にタブ文字がある行をコマンド行と解釈するようになっています

```Makefile:Makefile
hello:
	echo Hello > hello.txt
	echo World >> hello.txt
```

```bash:Terminal
make hello
```

```
echo Hello > hello.txt
echo World >> hello.txt
```

```bash:Terminal
cat hello.txt
```

```
Hello
World
```

# 疑似ターゲット

疑似ターゲットとして，実際に存在しないファイル名を指定することができます．
これは， ダミーターゲットと呼ばれたりもしますが，統一された用語はないようです．そこで，ここでは便宜上タスクまたはタスクターゲットと呼ぶことにします（これと対比させるため，ファイルをファイルターゲットと呼ぶことにしましょう）．タスクターゲットは，ある特定のファイルを作るためではなく，作業を行うコマンドとして利用したい場合に用いられます．

```
.PHONY: [実行したいタスク名]

[実行したいタスク名]:
	[そのタスクを行うためのコマンド行]
```

.PHONYは，タスクターゲットを宣言するためのターゲットです（phony: 偽の，まやかしの）．また，[そのタスクを行うためのコマンド行]の手前にもタブ1文字を入れることに注意してください．

タスクターゲットの例として，そのディレクトリの.classの拡張子をもつファイルをすべて削除するcleanターゲットを書いてみましょう．

```
.PHONY: clean
clean:
	rm -f *.class
```

こうすると，「make clean」ですべてのclassファイルが削除されます．

ところで，タスクターゲットは.PHONYターゲットを使わず作ることもできます．

```
clean:
	rm -f *.class
```

しかし，もしタスク名と同名のファイルやディレクトリがあると混乱しますので，.PHONYは積極的に書くようにしましょう．


# コメント

その前に少しコメントの書き方について解説しておきます．  
Makefileでは，`#`から行末までがコメントです．例えば，

```Makefile:Makefile
#
# Hello Worldを出力する
#
hello:
	echo Hello World!
```

のように使います．


# 改行

makeは基本的に行指向です．
見やすくするために改行したい場合はバックスラッシュ`\`を使って改行を無視させることができます．例えば，マクロの定義（後述）で

```Makefile:Makefile
object_files = \
   foo.o \
   bar.o \
   baz.o
```
と書けば，makeは改行を無視して次のように解釈します．

```
object_files = foo.o bar.o baz.o
```

ここで注意することは，foo.oだけをコメントアウトしたいために

```
object_files = \
#  foo.o \
   bar.o \
   baz.o
```

と書いてもダメだということです．これは，makeが

```
object_files = 
   bar.o baz.o
```

と解釈してエラーになってしまうためです．不便なので，なんとかならないでしょうかね（nmakeでは都合よく解釈してくれます）．




