---
title: "はじめに"
---

このあたりに関しては、過去記事の[GitLab CE インストール](https://zenn.dev/hr0t15/articles/98e421ccc894a9)をベースに記載する。


## GitLabについて

GitLab Inc.社が開発するGitリポジトリマネージャー。  
パブリックサービスである GitHub ライクなリポジトリマネージャーをローカルサイトで運用することができる。

コミュニティ版(CE:Community Edition)とエンタープライズ版(EE:Enterprise Edition)がある。

* CE版はMITライセンス、EE版は商用ライセンス。
* EE版をインストールした場合でも、CEに含まれる無料機能はすべて使用可能で、EEに含まれる有料機能を期間限定でお試し利用することができる。  
  お試し期間をすぎるとCE版機能のみを利用できるようになり、ライセンスを購入するとEE機能を利用できるようになる。
* [GitLabのドキュメント - 日本語](https://gitlab-docs.creationline.com/ee/index.html)

## ハードウェア要件

[Installation system requirements – GitLab Docs](https://docs.gitlab.com/ee/install/requirements.html)には、以下の通り示されている。

* ストレージ
  * Linuxパッケージのインストールには約2.5GBのストレージ容量が必要
* CPU
  * CPUの要件は、ユーザー数と予想される作業量に依存します。
  * CPUの推奨値: 最大20リクエスト/秒（RPS）または1000ユーザー - 8 vCPU。
* メモリ
  * メモリ要件は、ユーザー数と予想される作業負荷に依存します。
  * 推奨メモリ: 20リクエスト/秒（RPS）または1000ユーザーまで - 8GB（最小）、16GB（推奨）。


上記スペック上はかなりのハードルが高そうに見えるが、個人～4名程度の利用であれば、[Raspberry Piでの実行 - GitLab日本語マニュアル](https://gitlab-docs.creationline.com/omnibus/settings/rpi.html)にもある通り、Raspberry Pi 4 上でも稼働する。


## 導入環境

今回は以下の環境にて構築を行う。

* Proxmox VE上の仮想マシン(4 vCPU, メモリ8GB, ディスク200GB割り当て)
* Ubuntu Server 22.04
* GitLab CE 17.0.0

ここで、GitLab CEのインストールはOmnibus パッケージを用いて行う。
