# Kaggle-OTTO-Multi-Objective-Recommender-System

### 20221121

昨日までは酵素コンペをやろうと思っていたが、残り期間が1か月くらいで短いのと、EDAしても内容がよくわからんので
レコメンデーションやることにした。こっちのほうが過去コンペとか書籍とか初心者にとってはとっつきやすい。
まずは、公開notebookを参考にしてEDAに努める。

![image](https://user-images.githubusercontent.com/99288104/203073610-57aac5c7-5b32-44cf-aa32-b7487a5751e2.png)

## 方針

-Kaggle上で全てを完結させる<br>
-Version名は常にVersion○に統一して、README.mdに簡単に内容を書き込む<br>
-issue boardに思いついたアイデアを書いていく。<br>
-詳しい実験などを行った際には、その旨をREADMEに書き込み、詳しい実験結果はそのNoteBookの一番上にoverviewとして書き込む -> README.mdだけで事足りたので使いませんでした。<br>

## Basics
### Overview(DeepL)
 このコンペティションのゴールは、eコマースのクリック、カートへの追加、および注文を予測することである。あなたは、ユーザーセッションの過去のイベントに基づいて、多目的レコメンダーシステムを構築します。
あなたの仕事は、関係者全員のショッピング体験を向上させるのに役立ちます。顧客はよりカスタマイズされたレコメンデーションを受けることができ、オンライン小売業者は売上を伸ばすことができるかもしれません。

 オンラインショッピングでは、大手小売業者が提供する何百万もの商品の中から好きなものを選ぶことができます。しかし、あまりの種類の多さに圧倒され、カートを空にしてしまうこともあります。これでは、購入しようとする買い物客にも、売上を逃した小売業者にもメリットがありません。このような理由から、オンラインショップでは、買い物客の興味や動機に最も適した商品を案内するレコメンダーシステムが利用されています。データサイエンスを利用して、お客様が実際に見たい商品、カートに入れたい商品、注文したい商品をリアルタイムに予測する能力を高めれば、次にお気に入りの小売店でオンラインショッピングをするときの顧客体験を向上させることができます。
 現在のレコメンダーシステムは、単純な行列分解から変形型ディープニューラルネットワークまで、様々なアプローチのモデルで構成されています。しかし、複数の目的を同時に最適化できる単一のモデルは存在しません。このコンペティションでは、過去の同一セッションのイベントに基づいて、クリックスルー、カートへの追加、コンバージョン率を予測する単一エントリーを構築します。
 19,000以上のブランドから1,000万以上の商品を扱うOTTO社は、ドイツ最大のオンラインショップです。OTTOは、ハンブルクに拠点を置く多国籍企業Otto Groupの一員で、Crate & Barrel（米国）や3 Suisses（フランス）なども傘下に入れています。
 あなたの仕事は、オンライン小売業者が顧客のリアルタイムの行動に基づいて、膨大な種類の中からより適切な商品を選んで推薦できるようにすることです。レコメンデーションを改善することで、無限にあるように見える選択肢の中から、買い物客がより簡単に、より魅力的に商品を選べるようになります。

### Evaluation(DeepL)
投稿は、アクションタイプごとにRecall@20で評価し、3つのRecall値を加重平均しています。
![image](https://user-images.githubusercontent.com/99288104/203316754-dfdcd44c-1b9b-42ac-9cdd-fe0709098be1.png)

ここで、以下のように定義される。

![image](https://user-images.githubusercontent.com/99288104/203316888-33883f86-c324-4c6f-afcd-cd658fb0a2da.png)

はテストセット内のセッションの総数であり、 は各セッションタイプ（例えば、提出ファイルの各行）に対する予測値で、最初の 20 個の予測値の後に切り捨てられる。

 テストデータ内の各セッションについて、あなたのタスクは、テストセッションの最後のタイムスタンプの後に発生する各タイプの補助値を予測することです。言い換えれば、テストデータにはタイムスタンプで切り捨てられたセッションが含まれており、切り捨てられた時点以降に発生するものを予測することになります。
 クリックについては、各セッションのグランドトゥルース値は1つだけで、それはセッション中にクリックされた次の広告です（ただし、最大20広告値まで予測することができます）。カートとオーダーのグランドトゥルースは、セッション中にそれぞれカートに追加され、注文されたすべての援助値を含んでいます。
 
### data(DeepL)
 このコンペティションのゴールは、eコマースのクリック、カートへの追加、注文を予測することです。あなたは、ユーザーセッションの過去のイベントに基づいて、多目的レコメンダーシステムを構築します。
 学習データには、電子商取引のセッション情報がすべて含まれています。テストデータの各セッションについて、あなたのタスクは、テストセッションの最後のタイムスタンプtsの後に発生する各セッションタイプの補助値を予測することです。言い換えれば、テストデータには、タイムスタンプで切り捨てられたセッションが含まれており、切り捨てられた時点以降に何が起こるかを予測することになります。
 その他の背景については、公開されているOTTO Recommender Systems DatasetのGitHubを参照してください。
 
 ### ファイル
-train.jsonl - 完全なセッションデータを含むトレーニングデータです。<br>
-session - 一意のセッションID<br>
-events - セッション内で時間順に並べられたイベントの列です。<br>
-aid - 関連するイベントのアーティクル ID (製品コード)<br>
-ts - そのイベントの Unix タイムスタンプ。<br>
-type - イベントのタイプ、つまり、セッション中に製品がクリックされたか、ユーザーのカートに追加されたか、注文されたか。<br>
-test.jsonl - 切り捨てられたセッションデータを含むテストデータ<br>
 あなたのタスクは、セッションの切り捨て後にクリックされた次の商品と、カートや注文に追加された残りの商品を予測することです; あなたは、各セッションタイプについて最大20の値を予測することができます。
-sample_submission.csv - 正しいフォーマットのサンプル送信ファイルです。<br>

## Paper<br>

## Log

## 20221122
-まずは、Edward氏の公開noteboookのEDAを写経してみる。

##わかったこと
　-データ構造 🗂 
　　-session - 一意のセッションID。各セッションには、時間順に並んだイベントのリストが含まれる。
　　-events - セッション内の時間順に並んだイベントのリスト。各イベントは、3つの情報を含む。
　　-aid - 関連イベントのアーティクルID（製品コード
　　-ts - イベントの Unix タイムスタンプ (Unix time は、1970年1月1日 00:00:00 UTC からの経過したマイクロ秒の数)
　　-type - つまり、セッション中に製品がクリックされたか（clicks）、ユーザーのカートに入れられたか（carts）、注文されたか（orders）。　　
##疑問点
　-途中のsamplesizeはなぜ150000？
  -


