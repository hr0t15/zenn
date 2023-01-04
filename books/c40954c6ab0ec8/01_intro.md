---
title: "はじめに"
---


本書では、MLOpsの概要については触れない。  
(構築の記事を読む人はまずは触ったことのある人だろうし)

タイトルにもある通り、本書は実験管理にMLFlow Tracking、データワークフローにPrefectを導入する。

これらのコンポーネントを採用した背景について述べる。


# MLOpsのプロダクト

ここではプロダクト選定の基準について述べる。

## 実験管理

実験管理のプロダクトは主に以下がある。

- [MLflow](https://mlflow.org/) : オンプレ  
  MLflowは4つのコンポーネントから成っており、そのひとつであるMLflow Trackingが対象
- [ClearML](https://clear.ml/) : オンプレ/SaaS  
  複数コンポーネントから成り、その1機能として実験管理が可能
- [Neptune\.ai（neptune）](https://neptune.ai/) : SaaS
- [Weights & Biases（wandb）](https://wandb.ai/site) : SaaS
- [Comet](https://www.comet.ml/site/) : SaaS

比較記事としては「[Deepでポン用実験管理ツール（サービス）の比較2021](https://qiita.com/fam_taro/items/401ba82e710dca2781eb)」が詳しい。  

ここではオンプレを対象とするため、MLflowとClearMLのみが対象となる。  

ClearMLはDocker環境のみであり、多少複雑な構成となっている。
それに対してMLflowであれば、比較的シンプルな構成となる。

ゆえにまずはMLflowにより構築をする。

## データワークフロー

有名なデータワークフローのプロダクトは以下の通り。

- [Luigi](https://github.com/spotify/luigi)
- [Argo workflow](https://argoproj.github.io/argo-workflows/)
- [Apache Airflow](https://airflow.apache.org/)
- [Prefect](https://www.prefect.io/)

これらの違い、特にApache AirflowとPrefectの違いは「[次世代のワークフロー管理ツールPrefectでMLワークフローを構築する](https://developers.cyberagent.co.jp/blog/archives/38253/)」が詳しい。

今回は、上記記事に関わらず一般的に開発しやすいといわれるPrefectの導入を行う。

## ハイパーパラメータ管理

基本的にはベイズ最適化の実装である[Optuna](https://optuna.org/)と以下の組み合わせになる。

- argparse
- [Hydra](https://hydra.cc/)
- [omegaconf](https://github.com/omry/omegaconf)

この辺りの事情については「[機械学習の煩雑なパラメーター管理の決定版](https://logmi.jp/tech/articles/325087)」という記事が詳しい。  
なお、omegaconfとは、 Hydra が低レイヤー API として利用しており、Hydraの足回り的な作業は基本的にomegaconfによるものと認識して差し支えない。

管理対象のハイパーパラメータが多くなったり、カスタマイズ性の必要に応じて argparse -> Hydra -> omegaconf と使い分けるとよさそう。

今回はインストール対象には含めない。
