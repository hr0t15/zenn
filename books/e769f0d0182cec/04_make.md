---
title: "makeコマンド"
---

# コマンドの基本的な実行

make は

```bash:terminal
make
```

とだけタイプすることで、Makefile を読み込みターゲットの生成を開始する。  
このように何も指定せずに make を実行すると、make はその最終目標として Makefile の一番最初に書かれているターゲットを生成しようとする。  

例えば先の Makefile では、make は a.out という ターゲットを生成しようとする。 生成するターゲットを指定して make を実行するには、

```
make sub1.o 
```
のように実行すればよい。この場合、make は Makefile の一番最初に書いてある ターゲットのかわりに、「sub1.o」というターゲットを生成するにとどめる。
make がデフォルトで読み込むファイルは 「Makefile」 という名前でなければ ならない。しかし、それ以外のファイルを Makefile として利用したい場合は、


```
make -f Makefile.unix
```
のように指定する。これで、make は Makefile.unix というファイルを Makefile の代わりに使う。
シェル引数からのマクロ定義
また、make はマクロの定義をシェル引数からでもできる。

```
make CC=gcc
```

とすれば、make の実行時にはあらかじめ CC マクロが定義される。 この値は Makefile 中で定義されているマクロの値よりも優先して使われる。 こうすることで、Makefile を書きかえることなく手軽にいろいろな 状況でファイルを生成することができる。

# make の実行オプション

Make の実行オプションは普通シェルから指定するが、 環境変数 MFLAGS でも指定できる。これは多段 make (make がその中で さらに入れ子となった make を実行すること、多段 make 参照) で、親 make が実行オプションをその子 make に継承するために使う。



## `-f <filename>`

“Makefile”の代わりに別のファイル名を使う。  
“-”は標準入力。 また、Makefile 内から別の Makefile を取り込んだりする場合には、 サーチパスが設定できる。 マクロ VPATH は依存ファイルをサーチするパスのリストである。 「:」 で区切って書く。

## `-f <filename> <target>`

指定した`<filename>`に記述されているターゲット`<target>`を指定する。


## `-C <subdir>` 

サブディレクトリ`<subdir>`の中でmakeを実行する。


## `-n`

実行すべき生成コマンド列の表示のみを行い、実際には実行しない。 これは、ある Makefile がどんなことをするのか、前もって 予想したいときに有効。

## `-s`

make は生成コマンドを実行するときに、それを マクロ展開した状態で逐一表示しながら実行する。 このオプションは、実行するコマンドをいちいち表示させないようにする。

## `-k`

make は、生成コマンドを実行したときに、そこでエラーが発生すれば それ以降の生成を中止し、終了する。このオプションをつけると make は 生成コマンドでエラーが発生しても無視して、最後まで規則をやり通す。

## `-p`

make であらかじめ定義されている規則・マクロをすべて展開し表示する。

## `-t`
生成コマンドを何も実行せず、指定されたターゲットを touch する。

## `-e`
make 内で定義されたマクロよりも環境変数の値を優先する。 マクロの展開 の項を参照。


# Makefileから別のMakefileを実行する

Makefileから別のMakefileを実行する方法について。

ディレクトリ構造は以下のようなのを想定しています。

```
.
├── Makefile      <= 実行するMakefile
└── sub/
    └── Makefile  <= 呼び出されるMakefile
```

## include

別のMakefileをimportするには、includeを使います。

```Makefile:Makefile
include sub/Makefile
```

```Makefile:sub/Makefile
subprocess:
	@echo "This is a subprocess."
```

実行結果

```bash:terminal
make subprocess
```

```
This is a subprocess.
```

## 直接実行する場合

下のディレクトリに移動してから実行したい場合もあります。  
その時は `cd sub && make` とするのですが、実行してみると 

```
/bin/sh: line 0: cd: sub: No such file or directory
```
と怒られてしまいます。 

どうやら Makefile 内で実行する /bin/sh は相対パスで cd できない様なので、代わりに cd "$(PWD)/sub" && make と書きます。

```Makefile:Makefile
subprocess:
	cd "$(PWD)/sub" && make subprocess
```

```Makefile:sub/Makefile
subprocess:
	@echo "This is a subprocess."
```

実行結果

```bash:terminal
make subprocess
```

```
cd "path/to/sub" && make subprocess
This is a subprocess.
```