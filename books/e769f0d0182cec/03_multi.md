---
title: "複数ルールと依存性"
---

# 複数ルール

Makefileには，複数のルールを含めてかまいません．

```
hello.txt:
	echo Hello World > hello.txt

.PHONY: clean
clean:
	rm -f *.class
```


複数のルールを書いた場合には，makeを実行するときにターゲット名を指定するようにします．指定しなかった場合は，一番最初に書かれたルール（上の例ではターゲットがhello.txtのルール）が実行されます．したがって，このMakefileでcleanターゲットを実行するには，

```
$ make clean
```
とすればOKです．hello.txtの場合は，

```
$ make hello.txt
```

または

```
$ make
```

ということになります．さらに，cleanとhello.txtを同時に行いたい場合は，

```
$ make clean hello.txt
```
と複数のターゲットを指定します．

# 依存ターゲット

ここで改めてMakefileの構造をみるためにDocument Object Modelを書いてみましょう．

![](http://objectclub.jp/community/memorial/homepage3.nifty.com/masarl/article/gnu-make/rule/dom.png)

Makefile は複数のルールを持っています．ルールは，ターゲットとコマンド行からなります．個々のターゲットは実際のファイルまたはタスクです．

ここで，ターゲットの部分をもう少し詳しく見てみましょう．

![](http://objectclub.jp/community/memorial/homepage3.nifty.com/masarl/article/gnu-make/rule/target.png)

このように，各々のターゲットは複数の依存ターゲットを持っている場合があります．

依存ターゲットとは，そのターゲットを作るために必要なターゲット（部品）のことです．targetAというターゲットを作るためにtargetBという部品が必要な場合，Makefileを次のように書きます．

```
targetA: targetB
	[targetAのコマンド行]
```

これは「targetAの依存ターゲットがtargetBである」ということを表しています．

依存ターゲットがあれば，makeはターゲットの更新日時を比較してコマンド行を実行すべきかどうか判断します．つまり，依存しているものより更新日時が古ければ，コマンド行を実行してターゲットを最新にしようとするわけです．例えば，

```
targetA:targetB
	[targetAのコマンド行]

targetB:
	[targetBのコマンド行]
```

というMakefileで

```
$ make targetA
```
を実行するとどうなるか順に見てみましょう．

依存ターゲットtargetBを作成（＝[targetBのコマンド行]を実行）しようとします．

ただし，次の条件が成り立つときは何もしません．

- targetBがファイルターゲットかつ
- targetBという名前のファイルがすでに存在している場合

ターゲットtargetAを作成（＝[targetAのコマンド行]を実行）しようとします．  

ただし，次の条件が成り立つときは何もしません．

- targetAとtargetBがファイルターゲットかつ
- targetAとtargetBという名前のファイルが存在しており，
- targetAの更新日付がtargetBの更新日付より新しい場合

ポイントとなるのは，ファイルターゲットの場合実際のファイルの更新日時を比較している，ということです．  
もし更新日時が依存ターゲットより新しいならば，makeは何しません．ファイルやタスクの依存関係を記述し，適切な順番でコマンドを実行できるところがmakeの優れたところです．

依存ターゲットが複数ある場合は，

```
targetA: targetB targetC
	[targetAのコマンド行]
```

という風に書きます．このときtargetAの依存ターゲットはtargetBとtargetCの2つです．
