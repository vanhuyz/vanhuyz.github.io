+++
author = "Huy Van"
categories = ["Japanese", "Machine Learning"]
date = 2019-03-06T11:31:59Z
description = ""
draft = false
featured_image = "/content/images/2019/03/IMG_2740.JPG"
image = "/content/images/2019/03/IMG_2740.JPG"
slug = "acl-2018-report"
tags = ["Japanese", "Machine Learning"]
title = "自然言語処理の国際学会 ACL2018 @メルボルンに参加してきました！"

+++


7月10日~15日 で オーストラリアのメルボルンで開催されました [56th Annual Meeting of the Association for Computational Linguistics (ACL2018)](https://acl2018.org/) に参加してきました。

<!--more-->

# 経緯
ACL は一言で言うと「自然言語処理の世界トップコンファレンス」です。

私は最先端の対話システムはどうなっているのか気になって参加にいきました。今回はトップコンファレンス初参加です。



# ACL の日程と構成
日程は以下のと通りです。

7/15 (日) チュートリアル
7/16 (月) ~ 7/18 (水) メインカンファレンス
7/19 (木) ~ 7/20 (金) ワークショップ

メインカンファレンスは Long Papers, Short Papers, Posters に分かれています。たくさんのセッションがありますが、私は以下のセッションが気になります。

* Question Answering (質問応答）
* Dialog System (対話システム）
* Generation (文章生成）
* Machine Translation (機械翻訳)
* Summarization (機械要約）
* Vision (ビジョン)
* Machine Learning (機械学習)

自然言語処理学会なのに Vision というセッションがあって面白いですね。最近、言語 x 画像 というハイブリッド的な研究が結構増えているようです。

## 印象に残ったセッションと感想など

初日（7月10日）は[チュートリアルの日](https://acl2018.org/tutorials/)です。私が対話と強化学習関連の２つチュートリアルを聴講しました。

### T2: Neural Approaches to Conversational AI [[Slides](https://1drv.ms/b/s!AshEqwB44aR6k8wfrYIJEv2sbi9IFg)]
Deep Learning を使って対話システムにどう応用するのかという話です。対話システムは大きく2つに分かれて、「雑談」（りんなみたいに）と「タスクをこなすシステム」（Amazon Alexa や Google Assistant）です。古典的な対話システムは４つのコンポネン：「ユーザーの入力を理解するコンポネント」、「状態管理コンポネント」、「対話ポリシーコンポネント」と「レスポンス生成コンポネント」から成り立っていますが、これからすべて統一して、Deep Learning で end-to-end システムという研究が進んでいます。また、強化学習を導入していい感じにレスポンス返す研究も流行っているようです。。

### T7: Deep Reinforcement Learning for NLP [[Slides](http://www.cs.ucsb.edu/~william/DRL4NLP.html)]
強化学習 (Reinforcement Learning) はゲーム・ロボットなどに応用事例が多かったですが、最近のはやりで NLP (自然言語処理) にも導入されています。モチベーションは間接的な loss 関数 (cross entropy、hinge loss) の最小化の代わりに、直接最終的の指標 (BLEU、ROUGEなど）を最適化すると良いです。また、うまく設計できれば unlabeled data も使えるという利点があります。
強化学習は環境、エージェント、行動、報酬という４つの概念があります。例えば、Mario Run のゲームを例にすると、 環境はゲーム環境で、エージェントは Marioで、行動はジャンプで、報酬はコインの数と死亡（大きいマイナス点）です。ただし、NLP はゲームと違って、報酬は自明ではありません。報酬設計をミスすると、問題解けない可能性がお大きいので、報酬設計は現状課題となっています。最近、Inverse Reinforement Learning という研究分野もあって、報酬を自動的予測するとのことです。

**感想：** チュートリアルはよくまとまって、とても勉強になりました。

メインカンファレンスは印象に残ったのは以下です。

### Learning to Ask Good Questions: Ranking Clarification Questions using Neural Expected Value of Perfect Information [[paper](http://aclweb.org/anthology/P18-1255)] (University of Maryland, Microsoft Research)

best paper awards の 1 つです。質問応答タスクにおいて、今までのシステムは質問が来たら、必ず回答するとのことでしたが、人間はそれと違って、質問が曖昧のとき、質問者に明確にするため逆質問 (clarification question)することがあります。この研究ではそういう課題を発見し、[AskUbuntu](https://askubuntu.com/)などをベースにデータセットを作り、回答できるかどうかと良い逆質問を生成することです。

**感想：** 他の best paper awards と同様、新しいタスクを発見するのが今回評価が高いという印象です。モデルは特に breakthrough 的なものではなく、既存のニューラルネットワークを組むだけだと思います。

### Personalizing Dialogue Agents: I have a dog, do you have pets too?  [[paper](http://aclweb.org/anthology/P18-1205)] (Facebook AI Research)
今までの雑談システムは誰に対しても同じ回答でした。この研究では個人のプロフィールによって違うレスポンスするべきだと主唱し、データセットを作り、個人のプロフィールを memory network に保存し、それを基にいい感じにレスポンスするという仕組みです。

**感想：** これも新しいタスクを発見するという研究です。

### Efficient Large-Scale Neural Domain Classification with Personalized Attention [[paper](http://aclweb.org/anthology/P18-1206)] (Amazon Alexa)
今までの Alexa の built-in ファーストパーティドメイン（スキル）が多くなかったです。サードパーティスキルを使いたい場合は、「Alexa、〇〇につないで」みたいなコマンドが必要です。これから、コマンドが必要なくても正しいスキルが呼べるために研究しています。そうすると、分類ドメインが 10つ程度ではなく、数千ドメインとなっています。 例えば、単に「トマトで作れるレシピを教えて」だけで、正しくクックパッドスキルに振り分けるようになります。ユーザー数が多いのと装置のメモリ制限でいろんな課題があります。これで、shared encoder network でパラメータ減らすなどの工夫がありました。

また、クエリーの曖昧性、例えば「ピザが食べたい」の場合、「ピザ作りたい」なのか、「ピザ注文したい」なのか難しい問題です。それを解決するために、ユーザーの歴史から予測します。例えば、そのユーザーがピザ作ったことがなく、いつもピザ注文するなら単に今回ピザ注文するよねという予測します。それは personalized attention という仕組みです。

**感想：** 論文によると、tens of million ユーザーも対応できるようですが、これはもしかしてもうすでに本番に展開しているだろうか気になります。アカデミックよりではなく、サービスの観点から考えて、実際本番の問題を考慮して良いです。

### A Co-Matching Model for Multi-choice Reading Comprehension [[paper](http://aclweb.org/anthology/P18-2118)] (Singapore Management University, IBM Research)
muilti-choice 読解のタスクです。TOEIC を想像するとわかりやすいと思います。長い文章があって、それに関する質問に複数の候補回答の中から正しいものを選ぶタスクです。co-matching （文章と候補回答どのぐらいマッチングするのか attention mechanism で表す）を紹介し、RACE データセットで state of the art パーフォマンスを得たという内容です。

**感想：** attention mechanism がやはりまだ使い所が多いですね。

### MojiTalk: Generating Emotional Responses at Scale [[paper](http://aclweb.org/anthology/P18-1104)] (Tsinghua University, University of California - Santa Barbara)
対話システムで感情的なレスポンスを生成する研究です。データセットは Twitter からツイートとリプライのペアの収集し、絵文字が感情の "自然ラベル" とします。例えば、元々のツイートが「@amy  💗 miss you soooo much!!! 😭😭😭 」のとき、最後の絵文字 「😭」が感情のラベルとなります。その感情もパラメータとして、Conditional VAE (CVAE) と Reinforced CVAE でレスポンスを生成します。
例：

- query: add me in there my bro 🙏
- response:
  - 😜:i ’m not sure you ’ll be there
  - 💪: i ’m here for you
  - 💔: i ’m not ready for you

**感想：** 問題提議とデータ作りが面白いです。

### Hierarchical Neural Story Generation [[paper](http://aclweb.org/anthology/P18-1082)] (Facebook AI Research)
story 生成のタスクですが、2段階に分けて、まず prompt (主な単語の集合）を生成し、そこから story を生成します。例えば、
Prompt:
> The Mage, the Warrior, and the Priest

Story:

> A light breeze swept the ground, and carried with it still the distant scents of dust and time-worn stone. The Warrior led the way, heaving her mass of armour and muscle over the uneven terrain. She soon crested the last of the low embankments, which still bore the unmistakable fingerprints of haste and fear. She lifted herself up onto the top the rise, and looked out at the scene before her. [...]

Reddit からデータセットを構築し、それを用いて検証する研究です。prompt 生成は convolutional language model で、story 生成は convolutional seq2seq で行いました。 ここで CNN を使う理由は CNN が parallel 処理できるからです。特に、long story の生成に有利です。また、story 生成のとき、fusion model を提案し（a pretrained seq2seq +  another seq2seq) と self-attention を使うより、自然な story が生成されたという結果です。

**感想：** このタスクを気になる理由はレシピ生成と似ています。まず、食材 (≒prompt) を生成してから手順 (≒story) を生成することです。

### The Best of Both Worlds: Combining Recent Advances in Neural Machine Translation [[paper](http://aclweb.org/anthology/P18-1008)]  (Google)
機械翻訳は近年 seq2seq モデルから注目が集まって、それから Google がどんどん改善しています。最初の seq2seq モデルは RNMT (RNNベース) で、次に ConvS2S (convolutional ベース) になって、そして最近 Transformer (attention ベース) というモデルが一番性能が良かったです。この研究は ConvS2S と Transformer の良い点を抽出して RNMT に応用することで RNMT+ という新しいアーキテクチャができました。RNMT+ は RNMT、ConS2S、Transformer より性能が良いです。さらに、hybrid NMT model も提案し、encoder が Transformer から持ってきて、decoder が RNMT+から持ってくることで、RNMT+ より良い性能が得られます。

**感想：** たくさんのモデルの特徴を分析してから結合して state of the art パーフォマンスが得られるのが良いです。ただ、+1 BLEUスコアが significant かどうかはちょっと疑問です。

### Know What You Don’t Know: Unanswerable Questions for SQuAD [[paper](http://aclweb.org/anthology/P18-2124)] (Stanford University)
[SQuAD](https://rajpurkar.github.io/SQuAD-explorer/) は Stanford の定番の QA データセットです。文章があって、文章に関するいくつの質問・回答があります。このデータセットはすべての質問に対して必ず回答が文章の中にあるという設定です。この論文は SQuAD を拡張し、答えられない質問（回答が文章に入っていないもの）をいれて、さらに難易度を上げるということです。

**感想：** パッと見たらあまり面白くないと思いますが、機械翻訳はもう解けた問題とされて、Google の勝ちでしたが、QA (質問応答)はまだ解けない問題で、次のチャレンジになります。今いろんなところ頑張っています。SQuAD は QA の定番なので、優勝者は脚光を浴びる存在となるでしょう。

# その他の写真など
会場の入り口
<img src="/content/images/2019/03/IMG_2740-1.JPG" width="70%">

スポンサー
<img src="/content/images/2019/03/IMG_2783.JPG" width="70%">

メイン会場
<img src="/content/images/2019/03/IMG_2820.JPG" width="70%">


ポスター会場
<img src="/content/images/2019/03/IMG_2781.JPG" width="70%">

Manning 先生！
<img src="/content/images/2019/03/IMG_2864.JPG" width="70%">


ベトナム研究者たちと交流。私の向こう側に座っているのはまさかの [Thang Luong](https://ai.google/research/people/105176) さん - 今 Google AI の Research Scientist で、Google翻訳の裏の人。attention mechanism の[論文](http://www.aclweb.org/anthology/D15-1166)が有名。

<img src="/content/images/2019/03/37281016_10217389654681461_268088942333526016_n.jpg" width="70%">



# 小話
* メルボルンは真冬だったので、7度ぐらいでした。日本に戻ると急に35度になって、温度差ボケが大変でした。
* 今回始めて Airbnb を予約しました。部屋が広くてホテルより安く大変満足でしたが。。。夜中に水道管が壊れて焦ました。
* 学会に中国人がすごく多いという印象。何回も中国語で聞かれました。
* 外食が豊かで美味しい！いろんな国の料理のお店が並んでて毎日違うものが食べれるのが楽しかったです。洋風料理、日本料理、マレーシア料理、シンガポール料理、ベトナム料理、台湾料理いっぱい食べました。

