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

Nはテストセット内のセッションの総数であり、 predicted aidsは各セッションタイプ（例えば、提出ファイルの各行）に対する予測値で、最初の 20 個の予測値の後に切り捨てられる。

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
-まずは、Edward氏の公開noteboookのEDA(https://www.kaggle.com/code/edwardcrookenden/otto-getting-started-eda-baseline)
を写経してみる。(nb001)

##データ構造

-session - 一意のセッションID。各セッションには、時間順に並んだイベントのリストが含まれる。

-events - セッション内の時間順に並んだイベントのリスト。各イベントは、3つの情報を含む。

-aid - 関連イベントのアーティクルID（製品コード)

-ts - イベントの Unix タイムスタンプ (Unix time は、1970年1月1日 00:00:00 UTC からの経過したマイクロ秒の数)

-type - つまり、セッション中に製品がクリックされたか（clicks）、ユーザーのカートに入れられたか（carts）、注文されたか（orders）。
  
##ベースライン

 テストデータ`はトレーニングデータと同様にセッションを切り捨てたデータである。タスクは、セッションの切り捨て後にクリックされた次のエイドと、カートや注文に追加された残りのエイドを予測することであり、各セッションタイプについて最大20の値を予測することができる

 提出されたものは、各アクションタイプのRecallで評価され、3つのRecall値は重み付け平均される。{クリック': 0.10, 'カート': 0.30、'orders': 0.60} . 注文'の予測は、重みの大部分を占めるので、正しく行うことが重要です :)

 テストデータの各セッションについて、あなたのタスクは、テストセッションの最後のタイムスタンプの後に発生する各タイプの援助値を予測することです。言い換えれば、テストデータにはタイムスタンプで切り捨てられたセッションが含まれており、切り捨てられた時点以降に発生するものを予測することになります。

 クリックについては、各セッションのグランドトゥルース値は1つだけで、それはセッション中にクリックされた次の広告です（ただし、最大20広告値まで予測することができます）。カートと注文のグランドトゥルースは、セッション中にそれぞれカートに追加され、注文されたすべての援助値を含んでいます。

 各セッションとタイプの組み合わせは、提出物の中でそれ自身の session_type 行に表示される必要があり（セッションごとに 3 行）、予測はスペースで区切られる必要があります。これは、以下の sample_test_df で見ることができます。

## 20221123
-GUNES氏の公開noteboookのEDA(https://www.kaggle.com/code/gunesevitan/otto-multi-objective-recommender-system-eda)
を写経してみる。(nb002)

わかったこと(DeepL)

-1　トレーニングセットとテストセットは時間によって分割されています。トレーニングセットは4週間分のユーザーイベント、テストセットはトレーニングセットの翌週分のユーザーイベントです。トレーニングセットとテストセットの間でイベントが重複していないのは、リークを防ぐためにトレーニングセットのイベントの一部が切り取られているためです。イベントの種類の比率は、トレーニングセットとテストセットでほぼ同じであることが、以下の可視化からわかります。

-2　すでにご存知のように、トレーニングセットには1280万人のユニークユーザー（セッション）が存在し、テストセットには160万人のユニークセッションが存在します。しかし、その統計量もトレーニングセットとテストセットでは異なっています。トレーニングセットでは、平均セッション時間はテストセットの4倍です。トレーニングセットで最も短いセッションは2であり、これは単一イベントセッションでグランドトゥルースを作成することができないためです。トレーニングセットで最も長いセッションは500であり、これは極端に長いセッションを切り捨てるための閾値のように見えます。
　トレーニングセットは4週間であるのに対し、テストセットは1週間なので、セッション統計の不一致は問題ではないかもしれません。平均セッション時間がトレーニングセットで4倍長いのは、そのせいでもあります。
　セッションの持続時間の分布は、以下の可視化から見ることができます。どちらの密度も、解釈しやすいように対数スケールで表示されています。
 
 -3 トレーニングセットには180万個のユニークな商品(aid)があり、テストセットには78万個のユニークなaidがある。テストセットのaidはトレーニングセットのaidのサブセットなので、テストセットには未見のaidはない。また、トレーニングセットとテストセットではエイドの出現頻度分布が異なるが、これは前述の理由からである。トレーニングセットのエイドオカレンスの分布はより正常ですが、テストセットのエイドオカレンスの分布は多峰性で、これはセッションの切り捨てによるものだと思われます。
 以下の可視化から、エイドオキュレンスの分布を見ることができます。どちらの密度も、解釈しやすいように対数スケールで表示されています。
 
 -4  訓練とテストの援助頻度は非常によく似ています。テストセットはトレーニングセットの20倍近く小さいので、両者の間にはわずかな違いがある。トレーニングセットとテストセットを連結してエイド頻度を計算することは、エイド頻度を何らかの形で利用するのであれば、より信頼性が高いと思われる。
 トレーニングセット、テストセット、トレーニング＋テストセットについて、最も頻度の高いエイド上位20個を以下に可視化した。X軸は頻度、Y軸は括弧内のエイドとその頻度である。

 -5　トレーニングセットでは、平均でセッションの93%がクリック、16%がカート、10%が注文となっています。これらの数字は、すべてのセッションで計算された平均値であるため、100に加算されることはありません。標準偏差はそれぞれ12、10、8です。テストセットでは、セッションがランダムに切り捨てられるため、平均と標準偏差の数値が少し高くなります。イベントはテストセッションで過不足なく表現されていると思われます。(0 -> クリック, 1 -> カート, 2 -> 注文)
 
 -6　セッションの中には、クリック、カート、注文がないものもありますが、以下の統計には、それらの欠落したイベントタイプは含まれていません。ハイライトは、テストセットのセッションの中にはクリック、カート、注文しかないものがありますが、トレーニングセットのセッションはより異質なものであるということです。トレーニングセットの均質なセッションは、クリックのみのセッションです。この不一致はモデリング時に問題になる可能性があります。
 
 -7　もう一つ重要なことは、セッションの開始と終了の方法です。ここで興味深いことが2つあります。まず、セッションはクリックで始まるはずですが、カートまたは注文で始まるセッションはごくわずかです。これらのセッションは1％未満であり、選択した時間枠に収まるように、始まりから切り捨てられた可能性があります。もう一つは、テストセットのセッションは、切り捨てのため、注文で終わる可能性が低いということです。テストセットで切り捨てられたタイムステップは、注文イベントの可能性が高くなります。
 
 -8　1セッションが1タイムステップ以上であれば、トレーニングセットとテストセットの両方のGround-truthを作成することができます。クリックとその他のイベントのGround-truthは異なる方法で作成されます。あるタイムステップのクリックは、そのセッションの次のクリックがGround-truthとなります。あるタイムステップのカートとオーダーのGround-truthは、そのセッションの次のすべてのユニークなカートとオーダーのコレクションである。
 
 -9 プレシジョン（陽性予測値）とリコール（真陽性率）は、最もよく使われるバイナリおよびマルチクラス分類の指標である。精度の計算式は TP / (TP + FP)、リコールの計算式は TP / (TP + FN)である。精度を上げるには偽陽性をより少なく予測することが重要であり、リコールを上げるには真陽性をより多く予測することが重要です。これらの指標は常にトレードオフの関係にあるため、これらの指標の調和平均（F1スコア）も併用されている。
 これらの指標は情報検索問題でも利用されている。この問題では、予測値はイベントタイプごとにrecall@20で評価され、3つのrecall値にはそれぞれ指定された重みが掛け合わされる。イベントタイプの重みは、クリックは0.1、カートは0.3、注文は0.6である。この文脈では、偽陽性は偽陰性ほど重要ではないので、リコールはより理にかなっています。

 -10 テストセットのセッションは最後のイベントによって切り捨てられ、それらの切り捨てられたイベントのうち、いくつかが予測されると予想される。切り捨てられたイベントの種類に関係なく、クリック、カート、オーダーについて20の予測を行うことができる。リコールはこれらの20の予測に対して別々に計算され、イベントタイプに基づいて重み付けされます。最後に、3つのリコール値が平均化されます。
 例えば、セッション 747 がインデックス 59605 で切り捨てられ、そのイベントの支援を予測しようとし ていると仮定します。
 
  -11 クリックの基底真理は1つだけなので、recallは0か1のどちらかである。これは単純にin操作で評価することができる。もし、ground-truthのクリック支援がクリック予測にあれば、recallは1です。 そうでなければ、recallは0です。クリックrecallに0.1を掛ければ、0か0.1かのどちらかになります。

  -12 カートと注文のイベントタイプでは、複数のグランドトゥルース補助があるため、リコールは0から1の間の任意の値になる。これらのイベントタイプのリコールは集合演算で計算することができる。真陽性はGround-Truthと予測エイドの交点、偽陰性はGround-Truthと予測エイドの交点である。
 まれにGround-truthの数が20を超えることがある。そのような場合の計算ミスを防ぐため、分母はmin(20, n_aids)とする。

-CHRIS氏の公開noteboookのEDA(https://www.kaggle.com/code/cdeotte/time-series-eda-users-and-real-sessions/notebook)
を写経してみる。(nb003)

わかったこと(DeepL)

以上のように、多くのパターンが観察されます。ほとんどのユーザーが規則正しい行動をとっています。彼らは毎日同じ時間にクリック、カート、注文をします。また、ほとんどのユーザーは、毎週同じ日に買い物をするのが好きです。ほとんどのユーザーは、日中の起きている時間に活動していますが、夜、他のユーザーが寝ている間に買い物をするのが好きなユーザーもいます。また、ユーザーの買い物はクラスター化することもわかっています。このコンペティションでの課題は、（テストデータで提供された）最後のクラスタの残りを予測することと、（テストの最後のタイムスタンプの後の）新しいクラスタを予測することの両方が必要であることです。さらに、テストデータ（このノートには表示されていません）に含まれるすべてのユーザーのデータは1週間未満であるため、ユーザーの履歴情報がほとんどない状態でユーザーの行動を予測しなければなりません（つまり、RecSysの「コールドスタート」問題です）。ユーザーとその行動を理解することは、テストユーザーの将来の行動を予測することにつながるのです。


## 20221124
電車の中で読んだ『推薦システム実践入門』からの学び

-1　推薦アルゴリズムは以下3種類に分けられる。

　　-内容ベースフィルタリング　ユーザープロファイルとアイテム特徴との一致度を計算する
  
　　-協調フィルタリング（メモリーベース）-協調フィルタリング（モデルベース）
  
-2　協調フィルタリングはユーザーの過去の嗜好データがないと推薦できないため、コールドスタート問題に対応するなら内容ベースフィルタリングが適している。一方、予測精度を追求するなら様々なユーザーの行動履歴を推薦結果に反映できるため、協調フィルタリングが適している。ということは、今回のコンペではCHRIS氏のnotebookにあったようにコールドスタート問題が想定されるため、内容ベースフィルタリングをベースとしつつも、精度向上を志向すべく協調フィルタリングのモデルも作成し、これらをアンサンブルしてはどうか。

-3　今回コンペで登場するclick、cart、orderはいわゆる暗黙的評価値と考えられる。その場合の評価指標は「集合の評価指標」であり、PrecisionやRecallが代表的。クリックや購買の有無などの二値分類の精度の評価や推薦の幅広さを測るために利用される。

-4　暗黙的な評価値に対する行列分解としては、Implicit Matrix Factorization, Bayesian Personalizad Ranking, Factrization Machineがあるので3種類とも試したい。

-5　暗黙的な評価値を用いた推薦システムについては、「施策デザインのための機械学習入門」にも書いてるようなので購入予定

## 20221126
-CHRIS氏の公開noteboook(https://www.kaggle.com/code/cdeotte/candidate-rerank-model-lb-0-575)
を写経してみる。(nb004)

(以下DeepL）
手作りルールを用いた再ランク候補モデル
このノートでは、手作りのルールを使った「再ランク候補」モデルを紹介する。このモデルは、特徴量を設計し、アイテムやユーザにマージし、最終的に20を選ぶために再ランクモデル（XGBなど）を学習することで改善することができます。さらに、このノートブックを改良するために、ローカルなCVスキームを構築し、新しいロジックやモデルを実験する必要があります。

UPDATE: Radekのスキームを使って検証スコアを計算するノートブックをこちらで公開しました。

このコンペティションでは、「セッション」は実際にはユニークな「ユーザ」を意味することに注意してください。ですから、私たちの仕事は、1,671,803人のテスト「ユーザー」（すなわち「セッション」）のそれぞれが将来何をするかを予測することです。各テスト「ユーザー」（つまり「セッション」）に対して、残りの1週間のテスト期間中に何をクリックし、カートし、注文するかを予測する必要があります。

ステップ1 - 候補の生成
各テストユーザに対して、可能性のある選択肢、つまり候補を生成する。このノートでは、5つのソースから候補を生成しています。

-1　クリック、カート、注文のユーザー履歴

-2　テスト週で最も人気のあった20のクリック、カート、オーダー

-3　クリック・カート・注文とカート・注文の共訪問行列（タイプ重み付け付き

-4　カート/注文からカート/注文への共訪問行列（buy2buyと呼ぶ

-5　クリック/カート/注文からクリックへの共訪問マトリックス（時間重み付けあり

ステップ2 - 再ランク付けと20の選択
候補のリストがあれば、20個を予測対象として選択する必要があります。このノートでは、手作業で作成したルールでこれを行う。XGBoostモデルを学習させることで、予測値を向上させることができる。私たちの手作りルールは、以下を優先しています。

以前訪問した最も新しいアイテム
以前に複数回訪問したアイテム
以前カートまたは注文に入れたアイテム
カート/注文とカート/注文の共訪問マトリックス
現在人気のある商品

ステップ1 - RAPIDSによる候補の生成
候補生成のために、3つのCo-visitation Matrixを構築する。1つは、ユーザーの以前のクリック/カート/注文を考慮し、カート/注文の人気度を計算する。この行列にタイプウェイトを適用する。1つは、ユーザーの以前のカート/注文からカート/注文の人気を計算するものである。この行列を「buy2buy」行列と呼ぶ。ユーザーの前回のクリック/カート/注文があれば、クリックの人気度を計算する。この行列に時間の重み付けをする。これらの行列を高速に計算するために、RAPIDS cuDF GPUを使用する予定です!

RAPIDSで3つのコビジションマトリックスを計算する
GPU上のRAPIDS cuDFを使用して、3つのコビジションマトリクスを計算します。これは他の公開ノートブックのようにPandasのCPUを使うよりも30倍速いです! 最大速度のために、変数DISK_PIECESを、メモリエラーを発生させずに、使用しているGPUに基づいて可能な限り小さな数に設定します。32GB GPUラムでこのコードをオフラインで実行する場合、DISK_PIECES = 1を使用して、各共視行列をほぼ1分で計算することができます! KaggleのGPUは16GBのラムしか持っていないので、DISK_PIECES = 4を使用し、それぞれ3分という驚くべき時間がかかっています! 以下、計算を高速化するためのトリックをいくつか紹介します。

PandasのCPUの代わりにRAPIDSのcuDF GPUを使用する
ディスクを一度読み込んでCPUのRAMに保存し、後でGPUで複数回使用する。
GPUで一度に処理できる最大量のデータを処理する
データを2段階に分けてマージする 複数の小から単一の中へ 複数の中型から単一の大型へ
結果をディクショナリーではなく、パーケットとして書き込む

## 20221127
-今更ながら、githubにOtto自体が提供している情報があるのでそれを読んでみた。

-1　OTTOセッションデータセットは、多目的レコメンデーション研究を目的とした大規模データセットである。OTTOのウェブショップとアプリの匿名化された行動ログからデータを収集した。このデータセットのミッションは、セッションベースの推薦のベンチマークとして機能し、多目的およびセッションベースの推薦システム領域における研究を促進することです。また、ユーザーセッションの過去のイベントに基づいて、クリック、カート追加、注文を予測することを目標に、Kaggleコンペティションを開始しました。

-2　主な機能
1200万件の匿名化された実ユーザーセッション
クリック、カート、オーダーからなる2億2000万件のイベント
カタログに掲載された180万件のユニークな記事
すぐに使える.jsonl形式のデータ
多目的最適化のための評価指標

-3　
session - 一意のセッションID
events - セッション内の時系列に並んだイベント。
aid - 関連するイベントのアーティクル ID (製品コード) です。
ts - イベントのUnixタイムスタンプです。
type - イベントのタイプ、つまり、セッション中に製品がクリックされたか、ユーザーのカートに入れられたか、注文されたか。

-4　トレーニング/テスト分割
我々は、実際のウェブショップでそのようなシステムを展開する場合のように、将来におけるモデルのパフォーマンスを評価したいので、時間ベースの検証分割を選択します。我々のトレーニングセットは4週間のオブザベーションからなり、テストセットは翌週のユーザーセッションからなる。さらに、下図のようにテスト期間と重複するトレーニングセッションを切り捨てることで、将来的な情報漏えいを防止している。
　最終的なテストセットはKaggleコンペティションが終了した後に公開する予定です。しかし、それまでは、コンペティションの参加者は、トレーニングセッションから切り捨てられたテストセットを作成し、オフラインでモデルを評価するためにこれを使用することができます。この目的のために、testset.pyと呼ばれるPythonスクリプトが含まれています。

-5　メトリクスの計算
evalute.pyスクリプトを使用すると、各アクションタイプのRecall@20と投稿の加重平均Recall@20を計算することができます。


-CHRIS氏の公開notebook(https://www.kaggle.com/code/cdeotte/candidate-rerank-model-lb-0-575)
に出てくるCo-Visitation　Matrixの考え方がよくわからないので、VLADIMIR氏の公開notebook(https://www.kaggle.com/code/vslaykovsky/co-visitation-matrix)
を読んでみる

OTTO：コ・ビジテーション・マトリックス
よく見られている商品、よく買われている商品は存在する。ここでは、このアイデアを利用して、商品の共同訪問行列を計算する。計算方法は以下の通りです。

-1　まず、同じセッションの中で、時間的に近い（<1日）イベントのペアをすべて調べます。すべてのセッションで、各ペアのイベントペアのグローバル数をカウントすることによって、co-visitation行列Maid1,aid2を計算します。

-2　各 aid1 に対して，最も頻度の高い上位 20 個の aid2 を求める： aid2=argsort(M[aid])[-20:].

-3　テストセッションイベントのtail(20) (https://www.kaggle.com/code/simamumu/old-test-data-last-20-aid-get-lb0-947 参照)とco-visitation matrixから最も可能性の高いレコメンデーションを連結することによってテスト結果を生成する．これらのレコメンデーションは、セッションAIDとステップ2からのaid2から生成される。

## 20221129
 上記CHRIS氏のRERANKモデルのコードがいまいち理解できない。要はpythonがよくわかっていないということで、ここから少しだけpythonの文法を学ぶこととした。
 でも今日は休暇とったから、discussionに挙がっているトピックをざっと眺めてポイントをつかみたいと思う。
 
-1　分析の基本的な流れはCandidate Generation（特徴量の作成）→Ranking（モデリング）となる。なので、まずはどんな特徴量を生成するかだが、既購入品
再購入品
全体的に最も人気のある商品
ある種のクラスタリング技術に基づく類似アイテム
コ・ビジテーション・マトリクスなどに基づく類似項目
などがある。

　色々読み漁ってみたが、あまり進捗がなかった。分析の中身がよくわからないし、コードも何を書いているのかよくわからない。どっちからアプローチすればいいのかもわからない。ここはpythonの基本を押さえつつ、引き続きdiscussionとか読みつつ、粘り強くEDAを続けるしかないという次第。
 
## 20221201
　『Kaggleで勝つデータ分析の技術』の中で使えそうところをまとめる。
 
 -1　日付・時刻を表す変数の変換のポイント
 　-学習データとテストデータの分割
   -周期的な動きをとらえるための十分なデータがあるか
   -周期性を持つ変数の扱い
   
 -2　時間的な統計量をとる
   -Instacart Market Basket Analysisの2位のONODERAさんの解法では、ある商品について直近から注文したかどうかの配列が与えられ、その配列に直近のものから重みをつけて数値に変換するテクニックが使われていた。なお、これは今回のCHRIS氏のRERANKモデルでも使われている。
   
 -3　時系列データの扱い
 　-時間情報を持つ変数があるかどうか
   -学習データ・テストデータが時系列で分かれているか、時間に沿って分割したバリデーションを行う必要があるか
   -ユーザーや店舗といった系列ごとに時系列の目的変数があり、ラグ特徴量が取れる形式であるかどうか
   
## 20221203
　引き続き実際の分析コンペにおける特徴量作成の例
 -1　Santandar　Product　Recommendation
 　-各商品の購入有無フラグのラグ特徴量
 　-それらのフラグを連結したものをカテゴリ変数としてtarget-encoding
   -各商品の購入有無フラグが0→0、0→1、1→0、1→1に遷移した回数
   -各商品を購入していない状態が何か月続いているか
   -前月に購入した商品の例
  
 -2　Instacart Market Basket Analysis
   -ユーザーベースの特徴量
   -アイテムベースの特徴量
   -ユーザー×アイテムベースの特徴量
   -日時の特徴量
