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

# 代表的なモデル

## LSTM

- RNN の課題
  - 時系列を遡れば遡るほど、勾配が消失していく。
    - 長い時系列の学習が困難。
- 解決策
  - 前回の授業で触れた勾配消失の解決方法とは別で、構造自体を変えて解決したものが LSTM。
- 勾配爆発とは？
  - 勾配が、層を逆伝播するごとに指数関数的に大きくなっていく。

### CEC

- 勾配消失および勾配爆発の解決方法として、勾配が、1 であれば解決できる。

$$
\begin{aligned}
  \delta^{t-z-1} &= \delta^{t-z}
  \left\{
    Wf(u^{t-z-1})
  \right\}  = 1 \\[12px]
    \frac{\partial E}{\partial C^{t-1}}
    &= \frac{\partial E}{\partial C^t}  \frac{\partial C^t}{\partial C^{t-1}} \\[12px]
    &= \frac{\partial E}{\partial C^t}  \frac{\partial}{\partial C^{t-1}} \{ a^t-C^{t-1} \} \\[12px]
    &= \frac{\partial E}{\partial C^t}
\end{aligned}
$$

- CEC の課題
  - 入力データについて、時間依存度に関係なく重みが一律である。
    - ニューラルネットワークの学習特性が無いということ。

### 入力ゲートと出力ゲート

- 入力・出力ゲートの役割とは？
  - 入力・出力ゲートを追加することで、それぞれのゲートへの入力値の重みを、重み行列 $W$、$U$ で可変可能とする。
    - CEC の課題を解決。

### 忘却ゲート

- LSTM の現状
  - CEC は、過去の情報が全て保管されている。
- 課題
  - 過去の情報が要らなくなった場合、削除することはできず、保管され続ける。
- 解決策
  - 過去の情報が要らなくなった場合、そのタイミングで情報を忘却する機能が必要。

### 覗き穴結合 (peep hole)

- 課題
  - CEC の保存されている過去の情報を、任意のタイミングで他のノードに伝播させたり、あるいは任意のタイミングで忘却させたい。
  - CEC 自身の値は、ゲート制御に影響を与えていない。
- 覗き穴結合とは？
  - CEC 自身の値に、重み行列を介して伝播可能にした構造。

## GRU

- LSTM の課題
  - LSTM では、パラメータ数が多く、計算負荷が高くなる問題があった。
    - ⇒GRU
- GRU とは？
  - 従来の LSTM では、パラメータが多数存在していたため、計算負荷が大きかった。しかし、GRU では、そのパラメータを大幅に削減し、精度は同等またはそれ以上が望める様になった構造。
- メリット
  - 計算負荷が低い。

## 双方向 RNN

- 過去の情報だけでなく、未来の情報を加味することで、精度を向上させるためのモデル
- 実用例：文章の推敲や、機械翻訳等

## Word2vec

- RNN の課題:単語のような可変長の文字列を NN に与えることはできない。
  - 固定長形式で単語を表す必要がある。
- 学習データからボキャブラリを作成する。
- メリット
  - 大規模データの分散表現の学習が、現実的な計算速度とメモリ量で実現可能にした。

## Seq2Seq

- Seq2seq とは?
  - Encoder-Decoder モデルの一種を指す。
  - １文の一問一答に対して処理ができる、ある時系列データからある時系列データを作り出すネットワーク
- Seq2seq の具体的な用途とは？
  - 機械対話や、機械翻訳などに使用されている。

### Encoder RNN

- ユーザーがインプットしたテキストデータを、単語等のトークンに区切って渡す構造。
  - Taking：文章を単語等のトークン毎に分割し、トークンごとの ID に分割する。
  - Embedding ：ID から、そのトークンを表す分散表現ベクトルに変換。
  - Encoder RNN ：ベクトルを順番に RNN に入力していく。
- vec1 を RNN に入力し、hidden state を出力。この hidden state と次の入力 vec2 をまた RNN に入力してきた hidden state を出力という流れを繰り返す。
- 最後の vec を入れたときの hidden state を final state としてとっておく。この final state が thought vector と呼ばれ、入力した文の意味を表すベクトルとなる。

### Decoder RNN

- システムがアウトプットデータを、単語等のトークンごとに生成する構造。

1. Decoder RNN：Encoder RNN の final state (thought vector) から、各 token の生成確率を出力していきます final state を Decoder RNN の initial state ととして設定し、Embedding を入力。
1. Sampling: 生成確率にもとづいて token をランダムに選ぶ。
1. Embedding:`2`で選ばれた token を Embedding して Decoder RNN への次の入力とする。
1. Detokenize:`1-3`を繰り返し、`2`で得られた token を文字列に直す。

## HRED

- Seq2seq の課題
  - 一問一答しかできない
    - 問に対して文脈も何もなく、ただ応答が行われる続ける。
- HRED とは？
  - 過去 n-1 個の発話から次の発話を生成する。
    - Seq2seq では、会話の文脈無視で、応答がなされたが、HRED では、前の単語の流れに即して応答されるため、より人間らしい文章が生成される。
  - Seq2Seq+ Context RNN
    - Context RNN: Encoder のまとめた各文章の系列をまとめて、これまでの会話コンテキスト全体を表すベクトルに変換する構造。
      - 過去の発話の履歴を加味した返答をできる。
- HRED の課題
  - HRED は確率的な多様性が字面にしかなく、会話の「流れ」のような多様性が無い。
    - 同じコンテキスト（発話リスト）を与えられても、答えの内容が毎回会話の流れとしては同じものしか出せない。
  - HRED は短く情報量に乏しい答えをしがちである。
    - 短いよくある答えを学ぶ傾向がある。

## VHRED

- VHRED とは？
  - HRED に、VAE の潜在変数の概念を追加したもの。
    - HRED の課題を、VAE の潜在変数の概念を追加することで解決した構造。

## VAE

### オートエンコーダー（自己符号化器）

- オートエンコーダとは？
  - 教師なし学習の一つ。 そのため学習時の入力データは訓練データのみで教師データは利用しない。
- オートエンコーダ具体例
  - MNIST の場合、28x28 の数字の画像を入れて、同じ画像を出力するニューラルネットワークということになります。
- オートエンコーダ構造説明
  - 入力データから潜在変数 z に変換するニューラルネットワークを Encoder 逆に潜在変数 z をインプットとして元画像を復元するニューラルネットワークを Decoder。
- メリット
  - 次元削減が行えること。

## Attention Mechanism

- seq2seq の課題: 長い文章への対応が難しい
  - 文章が長くなるほどそのシーケンスの内部表現の次元も大きくなっていく、仕組みが必要になる。
- 「入力と出力のどの単語が関連しているのか」の関連度を学習する仕組み。
- Attention というメカニズムによって、必要な情報だけに「注目」を向けさせることができます。
