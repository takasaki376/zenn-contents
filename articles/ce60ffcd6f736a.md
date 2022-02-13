---
title: "Deep Learning資格試験 深層学習 再帰的ニューラルネットワーク"
emoji: "🐷"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["E資格"]
published: true
---

# はじめに

日本ディープラーニング協会の Deep Learning 資格試験（E 資格）の受験に向けて、調べた内容をまとめていきます。

# 概要

- 時系列データに対応可能なニューラルネットワークである。
- 時系列データとは？
  - 時間的順序を追って一定間隔ごとに観察され、しかも相互に統計的依存関係が認められるようなデータの系列
- 具体的には？
  - 音声データ
  - テキストデータ
  - 年度ごとの売上データ
  - １時間ごとの降水量
  - 自然言語
- 数学的記述

$$
\begin{aligned}
  u^t &= W_{(in)} x^t + Wz^{t-1} + b \\[8px]
  z^t &= f\left( W_{(in)} x^t + Wz^{t-1} + b \right) \\[8px]
  v^t &= W_{(out)} z^t + c \\[8px]
  y^t &= g \left(W_{(out)} z^t + c \right)
\end{aligned}
$$

$f$、$g$：活性化関数 \
$u$：活性化関数を通す前の入力 × 重み \
$z$：$u$を活性化関数を通した後の値 \
$v$：活性化関数を通す前の z× 重み \
$y$：$v$を活性化関数を通した後の値

# BPTT (Backpropagation Through Time)

- BPTT とは、RNN においてのパラメータ調整方法の一種　 ⇒ 　誤差逆伝播の一種
- 誤差が時間をさかのぼって逆伝播する

## 数学的記述

### 重み

$$
\begin{aligned}
  \frac{\partial E}{\partial W_{(in)}} &= \frac{\partial E}{\partial u^t}
    \left[
      \frac{\partial u^t}{\partial W_{(in)}}
    \right]^T \\[12px]
  &= \delta^t[x^t]^T \\[12px]
  \frac{\partial E}{\partial W_{(out)}} &= \frac{\partial E}{\partial v^t}
    \left[
      \frac{\partial v^t}{\partial W_{(out)}}
    \right]^T \\[12px]
  &= \delta^{out, t}[z^t]^T \\[12px]
  \frac{\partial E}{\partial W} &= \frac{\partial E}{\partial u^t}
    \left[
      \frac{\partial u^t}{\partial W}
    \right]^T \\[12px]
  &= \delta^t[z^{t-1}]^T \\[12px]
\end{aligned}
$$

### バイアス

$$
\begin{aligned}
  \frac{\partial E}{\partial b} &= \frac{\partial E}{\partial u^t}
  \frac{\partial u^t}{\partial b} \\[12px]
  &= \delta^t \\[12px]
  \frac{\partial E}{\partial c} &= \frac{\partial E}{\partial v^t}
  \frac{\partial v^t}{\partial c} \\[12px]
   &= \delta^{out, t}
\end{aligned}
$$

### パラメータ更新

#### 重み

$$
\begin{aligned}
  W_{(in)}^{t+1} &= W_{(in)}^t-\epsilon\frac{\partial E}{\partial W_{(in)}} \\[12px]
  &= W_{(in)}^t-\epsilon\sum_{z=0}^{T_t}{\delta^{t-z}[x^{t-z}]^T} \\[12px]
  W_{(out)}^{t+1} &= W_{(out)}^t-\epsilon\frac{\partial E}{\partial W_{(out)}} \\[12px]
  &= W_{(out)}^t-\epsilon \delta^{out,t}[x^{t}]^T \\[12px]
  W^{t+1} &= W^t-\epsilon\frac{\partial E}{\partial W} \\[12px]
  &= W^t-\epsilon\sum_{z=0}^{T_t}{\delta^{t-z}[x^{t-z-1}]^T} \\[12px]
\end{aligned}
$$

#### バイアス

$$
\begin{aligned}
  b^{t+1} &= b^t-\epsilon\frac{\partial E}{\partial b} \\[12px]
  &= b^t-\epsilon\sum_{z=0}^{T_t}{\delta^{t-z}} \\[12px]
  c^{t+1} &= c^t-\epsilon\frac{\partial E}{\partial c} \\[12px]
  &= c^t-\epsilon \delta^{out,t}
\end{aligned}
$$

# 制度指標

## BLEU

機械翻訳の精度を算出する際、英文では$n=1 \sim 4$における、modified n-gram precision の値の対数加重平均と BP を用いる。具体的には以下の式を用いて算出する。

$$
BLEU = BP \cdot exp \left( \sum_{n=1}^N \frac{1}{N} logP_n \right)
$$

加重平均でなく、対数加重平均を用いるメリットとして、機械翻訳の妥当性と流暢性を考慮した安定的な評価ができる。
modified n-gram precision は n について指数的に小さくなる傾向がある。そのため、対数加重平均をとってスコアを平坦化している。n が小さい時のスコアは妥当性、n が大きい時のスコアは流暢性を評価していると考えることができる。

### Modified precision

全提案訳文の全 n-gram について、$Count$と$Count_{dp}$の合計の比 \
　 ⇒ 全 n-gram のうち、どれぐらい参照翻訳の中にある？

$$
\begin{aligned}
P_n = \frac{\sum_{C\in E} \sum_{n-gram \in C} Count_{dp}(n-gram)}{\sum \sum Count(n-gram)}
\end{aligned}
$$

### Brevity penalty（BP）

機械翻訳と参照翻訳の長さを考慮する

$$
\begin{aligned}
BP = e^{min(0 , 1-\frac{r}{c})}
\end{aligned}
$$

c：提案訳文の長さ \
r：Ref の文の長さ

$$
\begin{aligned}
    BP =
    \begin{cases}
        1  &(c \geq r) \\
        e^{1-\frac{r}{c}} &(c<e) \\
    \end{cases}
\end{aligned}
$$

# 自然言語処理

## Word2vec

- RNN の課題:単語のような可変長の文字列を NN に与えることはできない。
  - 固定長形式で単語を表す必要がある。
  - 全ての言語を表現しようとすると次元数が膨大になり、計算量が増える。（One-hot ベクトル表現とその限界）
- 学習データからボキャブラリを作成する。
- 単語をベクトル化し、似た概念同士では、似たベクトルとする。(**単語分散表現**)
  - **ベクトルの内積**は類似度を表す。
    - ２つのベクトルが同じ向き：内積**大**
    - ２つのベクトルが関係ない向き：0
    - ２つのベクトルが逆の向き：内積**マイナスの大**
- メリット
  - 大規模データの分散表現の学習が、現実的な計算速度とメモリ量で実現可能にした。

## モデル

### CBOW (Continuous Bag-of-Words Model)

- 入力として周辺の単語を与え、中心語を予測する。

### Skip-Gram（Continuous Skip-Gram Model）

- 入力として中心語を与え、その周辺語の予測する。

## 負例サンプリング

- 損失関数の計算時間の短縮を図る。
- **CBOW** と **Skip-Gram** における出力層では、全ての語彙を対象として、ソフトマックス関数と交差エントロピー誤差関数の計算を行う。
- **負例サンプリング**を適用した場合の出力層では、１個の正例と$k$個の負例だけを対象としてシグモイド関数と交差エントロピー誤差関数の計算を行う。
- **CBOW** と **Skip-Gram** における出力層では、多クラス分類問題を解いているが、**負例サンプリング**を適用した場合は$k+1$個の２クラス分類問題を解いている。
- $k$個の負例は、コーパスの中から単語の出現頻度に基づいてサンプリングする。
