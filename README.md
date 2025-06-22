
# 名前分類モデルの構築（RNN / LSTM / GRU / BERT）

## 目的
- 名前のスペル（文字列）から、その名前が属する言語（Japanese, English など）を分類するモデルを構築。
- RNNベースのモデルとBERTモデルを比較し、それぞれの特徴と精度を確認。

---

## 使用データ
- `data/names/*.txt` に言語ごとの名前リスト（例: Japanese.txt, English.txt など）
- 各行に1つの名前が記載されており、合計18言語、2万件以上の名前を使用

---

## モデルとアプローチ

| モデル  | 処理単位 | 特徴 |
|--------|-----------|------|
| RNN    | 文字      | 単純な構造だが比較的まともな分類精度 |
| LSTM   | 文字      | 勾配消失に強く、長期依存も学習可能だが学習が安定しにくい |
| GRU    | 文字      | LSTMよりシンプルで、精度・安定性ともに高い傾向 |
| BERT   | 文字（文字レベルtokenizer） | トランスフォーマーベース、精度は高いが学習時間が長い |

---

## トークン化（文字単位）

- `tokenizers` を用いて文字単位のトークナイザを構築
- 独自 vocab を JSON 形式で構成し、DistilBERT に適用

---

## 推論例（top-3予測）

| 名前     | RNNの予測                          | LSTMの予測                            | GRUの予測                          |
|----------|-----------------------------------|--------------------------------------|-----------------------------------|
| Dovesky  | Russian (-0.76), Czech (-1.11), Polish (-2.29) | Czech (-2.83), English (-2.84), Spanish (-2.84) | **Russian (-0.26)**, Czech (-1.62), Polish (-4.33) |
| Jackson  | **Scottish (-0.13)**, English (-2.80), Russian (-3.94) | Italian (-2.83), Czech (-2.83), Japanese (-2.84) | **Scottish (-0.21)**, English (-2.29), Czech (-3.56) |
| Satoshi  | **Japanese (-1.26)**, Polish (-1.63), Italian (-1.73) | Czech (-2.83), Italian (-2.84), Japanese (-2.84) | Italian (-0.88), Polish (-1.12), **Japanese (-3.03)** |

---

## 精度評価（BERT）

- Accuracy, Precision, Recall を `sklearn.metrics` で測定
- BERT学習は `transformers.Trainer` により実施

---

## 結論
- RNNでも一定の精度を出せるが、GRUの方が安定して高精度
- BERTは非常に精度が高いが、文字レベルトークナイザの設計と訓練コストに注意が必要

---
## 注意点
- このプロダクトはgoogle colabを用いて制作しましたがgit hubにコピーが上手く成功せず、コードを確認したい場合はgit cloneしてから確認お願いします。

---

## 参考資料
- [BERTを使った名前分類タスクの実装例](https://nagomi-informal.net/archives/2530)
- [PyTorchによる文字分類チュートリアル](https://pytorch.org/tutorials/intermediate/char_rnn_classification_tutorial.html)
