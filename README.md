## Attention is All you Need (Transformer)

This repository implements the `transformer` model in *pytorch* framework which was introduced in the paper *[Attention is All you Need](https://arxiv.org/abs/1706.03762)* as described in their
NIPS 2017 version: https://papers.nips.cc/paper/7181-attention-is-all-you-need.pdf


The overall model architecture is as shown in the figure:

![][transformer]

[transformer]: img/transformer.png "Transformer Model"


The code in this repository implements the following features:
* Positional Encoding
* Multi-Head Dot-Product Attention
* Positional Attention from "*[Non-Autoregressive Neural Machine Translation](https://arxiv.org/abs/1711.02281)*"
* Label Smoothing
* Warm-up steps based training of Adam Optimizer
* LayerNorm and residual connections after each sublayer
* Shared weights of target embedding and decoder softmax layer
* Beam Search (Experimental)

## Software Requirements
* Python 3.6
* Pytorch v0.4 (needs manual installation from source https://github.com/pytorch/pytorch)
* torchtext
* numpy

One can install the above packages using the requirements file.
```bash
pip install -r requirements.txt
```


## Usage

### Step 1: Preprocessing:
```bash
python preprocess.py -i data/ja_en -s-train train-big.ja -t-train train-big.en -s-valid dev.ja -t-valid dev.en --save_data demo
```

### Step 2: Train and Evaluate the model:
```bash
python train.py -i data/ja_en --data demo --gpu 0 --batchsize 128 --tied --beam 5 --dropout 0.2 --epoch 40 --layer 1 --multi_heads 8
```

## Dataset

Dataset Statistics included in `data` directory are:

| Dataset                     |Train Set|Dev Set|Test Set|
| --------------------------- |:-------:|------:|-------:|
| Japanse-English             | 148,850 | 500   | 500    |
| IWSLT'15 English-Vietnamese | 133,317 | 1,553 | 1,268  |
| IWSLT'16 German-English     | 98,132  | 887   | 1,565  |


## Experiments
All the experiments were performed on a modern Titan-Xp GPU with 12GB RAM.
BLEU Scores are computed using Greedy Decoding.

### Ja->En translation
[Dataset URL](https://github.com/neulab/xnmt/tree/master/examples/data)

| Method                  | Layers | BLEU (dev) | BLEU (test)  | Parameters | Words / Sec |
| -----------------------------|:-:|:-----:| :----: |------:| -----:|
| Transformer (self)           | 1 | 32.10 |     |32.5 M | 60.1K |
| Transformer (self)           | 6 | 34.65 |     |69.3 M | 15.5K |
| BiLSTM encoder (OpenNMT-py)  | 1 | 29.55 |     |41.3 M | 31.5K |
| LSTM encoder (OpenNMT-py)    | 1 | 30.15 |     |41.8 M | 35.5K |
| Transformer (OpenNMT-py)     | 1 | 26.83 |     |42.3 M | 52.5K |
| BiLSTM encoder (XNMT)        | 1 | 29.58 | 31.39 |   | 9.1K<sup>*</sup> (Target Words) |
| Transformer (XNMT)           | 1 | 25.55 |      |   | 2.2K (Target Words) |

<sup>*</sup>1 epoch get completed in around 180 seconds.

Some more results:

- **31.33** (Layers=1, B=100, Beam=5)
BLEU: 31.33, 63.8/38.4/26.9/19.9 (BP = 0.923, ratio=0.93, hyp_len=4222, ref_len=4557)
- **32.91** (Layers=1, B=128, Beam=5)
BLEU: 32.91, 63.2/39.2/28.4/21.3 (BP = 0.939, ratio=0.94, hyp_len=4289, ref_len=4557)
- **31.70** (Layers=1, B=156, Beam=5)
BLEU: 31.70, 60.4/36.6/25.9/19.5 (BP = 0.975, ratio=0.98, hyp_len=4444, ref_len=4557)
- **32.56** (Layers=1, B=100, Beam=5, Pos_Attention=True)
BLEU: 32.56, 62.0/37.7/27.1/20.6 (BP = 0.963, ratio=0.96, hyp_len=4391, ref_len=4557)
- **34.65** (Layers=6, B=100, Beam=5)


### En->Vi translation
[Dataset URL](https://nlp.stanford.edu/projects/nmt/)

| Method                 | Layers | BLEU (dev)| BLEU (test)  |Parameters| Words / Sec |
| --------------------------- |:-:|:----: |:----: |------:| ----:|
| Transformer (self)          | 1 | 21.96 |       | 41.2 M | 57.8K |
| Transformer (self)          | 2 | 22.96 |       | 48.5 M | 40.2K |
| BiLSTM encoder (OpenNMT-py) | 1 | 21.99 |       | 53.5 M | 30.5K |
| LSTM encoder (OpenNMT-py)   | 1 | 21.04 |       | 53.9 M | 29.5K |
| Transformer (OpenNMT-py)    | 1 | 19.26 |       | 55.3 M | 48.5K |
| BiLSTM encoder (XNMT)       | 1 | 21.31 | 23.87 |        | 7.2K (Target Words) |
| Transformer (XNMT)          | 1 |       |       |        |


### De->En translation (Dev Set BLEU Scores)
[Dataset URL](http://www.phontron.com/class/mtandseq2seq2017/) . This dataset exists in tokenised form (using NLTK and lowercase).

| Method                 | Layers | BLEU (dev) | BLEU (test) | Parameters  | Words / Sec |
| --------------------------- |:-------------:|:---:|:---: | -----:| ----:|
| Transformer (self)          | 1 | 21.91  |       |  54.5 M |  44.5K  |
| Transformer (self)          | 2 |        |       |  |
| BiLSTM encoder (OpenNMT-py) | 1 | 23.10  | 23.71 |  73.7 M |  |
| LSTM encoder (OpenNMT-py)   | 1 |        |       |  |
| Transformer (OpenNMT-py)    | 1 |        |       |  |
| BiLSTM encoder (XNMT)       | 1 | 22.87  | 23.43 |  | 8K |
| Transformer (XNMT)          | 1 |        |       |  |

[//]: <> (git checkout 78acbe019f91e2e41b1975e1a06e9519d66a48a4 , "eval" branch, for best BLEU Scores)

## Acknowledgements
* Thanks to the suggestions from Graham Neubig [@gneubig](https://github.com/neubig) and Matt Sperber [@msperber](https://github.com/msperber)
* The code in this repository was originally based and has been adapted from the [Sosuke Kobayashi](https://github.com/soskek)'s implementation in Chainer "https://github.com/soskek/attention_is_all_you_need".
* Some parts of the code were borrowed from [XNMT](https://github.com/neulab/xnmt/tree/master/xnmt) (based on [Dynet](https://github.com/clab/dynet)) and [OpenNMT-py](https://github.com/OpenNMT/OpenNMT-py) (based on [Pytorch](https://github.com/pytorch/pytorch)).