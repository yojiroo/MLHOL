# Azure Machine Learning を使用したスマート アプリの構築
## 概要
機械学習は、コンピューター科学の中で最も急速に成長している分野の 1 つです。大量のデータを使用する予測分析を、それらのデータから繰り返し学習するアルゴリズムを採用することによって簡易化します。機械学習の使用範囲は、クレジットカード不正使用の検出や自動運転車から、光学文字認識 (OCR) やオンライン ショッピングでの商品のお勧めまで、多岐にわたります。機械学習は、コンピューターをよりスマートにすることによって、ユーザーをよりスマートにします。また、より多くのデータが使用可能になるにつれ、機械学習の有用性は増すばかりで、そのデータから予測分析を行いたいという要望も大きくなってきています。
Azure Machine Learning は、クラウドベースの予測分析サービスであり、あらゆるスキル レベルのデータ科学者に、効率化されたエクスペリエンスを提供します。Azure Machine Learning と共に、Azure Machine Learning Studio (ML Studio) が提供されています。これは、ブラウザーベースのツールであり、機械学習モデルを構築する際に、使いやすいドラッグ アンド ドロップ インターフェイスを提供します。これには、時間を節約できる実験のライブラリが搭載されており、Bing などの Microsoft ビジネス ソリューションによって実世界で開発およびテストされたクラス最高のアルゴリズムが提供されます。また、R および Python のサポートが組み込まれているので、カスタム スクリプトを作成して、モデルをカスタマイズすることができます。ML Studio でモデルを構築し、トレーニングを行った後は、そのモデルをさまざまなプログラミング言語で使用可能な Web サービスとして簡単に公開することも、Cortana Intelligence ギャラリーに置いて、コミュニティで共有することもできます。
このラボでは、Azure Machine Learning を使用して、手書きの数字を認識するモデルの構築、トレーニング、およびスコア付けを行います。学術調査用に公開されている実際の OCR データ セットを使用します。モデルを Web サービスとして展開した後は、画面に数字を描けるように Electron クライアントを作成します。その後、描いた数字を Azure Machine Learning が識別できるかどうか確認します。モデルを構築してトレーニングを行う方法、およびそのモデルを利用するコードを記述する方法を学びます。

## 目的
このハンズオン ラボでは、以下の方法について学習します。
+ Azure Machine Learning Studio を使用して、モデルの構築、トレーニング、およびスコア付けを行う。
+	モデルを Web サービスとして展開し、コードまたはスクリプトからアクセスできるようにする。
+	作成したアプリから ML Web サービスを呼び出す。
## 前提条件
このハンズオン ラボを完了するには、以下が必要です。
+	アクティブな Microsoft Azure サブスクリプション。これがない場合は、無料試用版にサインアップしてください。
+	Visual Studio Code。
+	Node.js。
## 演習
このハンズオン ラボでは次の演習を行います。
+ 演習 1:  Machine Learning の実験を作成する
+	演習 2:  データセットをアップロードする
+	演習 3:  分類モデルのトレーニングを行う
+	演習 4:  モデルにスコアを付ける
+	演習 5:  モデルを Web サービスとして展開する
+	演習 6:  Electron クライアントを構築する
+	演習 7:  モデルをテストする
このラボの推定所要時間: 60 分
## 演習 1: Machine Learning の実験を作成する
Azure Machine Learning を導入する最初のステップは、ML ワークスペースと、そこで行う実験の作成です。この演習では、Azure ML Studio で Machine Learning の実験を稼働させます。

1.	ブラウザーで Azure ポータルを開きます。ログインするよう求められたら、Microsoft
 アカウントを使用してログインします。
2.	[+ New]、[Intelligence + analytics]、[Machine Learning Webサービス],[ワークスペースの表示].[追加] の順にクリックします。

![1](https://github.com/yojiroo/MLHOL/blob/master/Azure%20Machine%20Learning(Node.js)/MLHOL_nodejs_img/1.png)
ML ワークスペースの作成
![2](https://github.com/yojiroo/MLHOL/blob/master/Azure%20Machine%20Learning(Node.js)/MLHOL_nodejs_img/2.png)
ワークスペースの表示
![3](https://github.com/yojiroo/MLHOL/blob/master/Azure%20Machine%20Learning(Node.js)/MLHOL_nodejs_img/3.png)
追加画面
3.	[Machine Learning ワークスペース] ブレードで、"DXLabs" などのワークスペース名を入力し、緑色のチェック マークがその横に表示されることを確認します。[Resource group] の [Create new] をクリックして、"DXLabsResourceGroup" などのリソース グループ名を入力します (二重引用符は付けません)。[Location] で、最も近くの場所を選択します。次に、ストレージ アカウント名を入力して、ここでもその横に緑色のチェック マークが表示されることを確認します。
ストレージ アカウント名は、3 ～ 24 文字の長さで、数字と小文字のみを使用でき、Azure 内で一意である必要があります。名前の横の緑色のチェック マークは、これらの条件を満たしていることを表します。
![4](https://github.com/yojiroo/MLHOL/blob/master/Azure%20Machine%20Learning(Node.js)/MLHOL_nodejs_img/4.png)
MLワークスペースの構成
4.	[Web サービス プラン価格レベル] をクリックして、[DEVTEST 標準] を選択します。 [Machine Learningワークスペース] ブレードの下部にある [ダッシュボードにピン留めする] をオンにし、[Create] ボタンをクリックして完了します。
![5](https://github.com/yojiroo/MLHOL/blob/master/Azure%20Machine%20Learning(Node.js)/MLHOL_nodejs_img/5.png)
価格レベルの選択
5.	通常、新しい ML ワークスペースの作成には 30 秒ほどかかります。展開が完了したら、[Launch Machine Learning Studio の起動] をクリックします (次に示した画面が自動的に表示されない場合は、ダッシュボードで ML ワークスペース用に作成されているタイルをクリックして、表示します)。
![6](https://github.com/yojiroo/MLHOL/blob/master/Azure%20Machine%20Learning(Node.js)/MLHOL_nodejs_img/6.png)
ML Studio の起動
6.	[Sign in here] をクリックし、Microsoft アカウントを使用して ML Studio にサインインします。
![7](https://github.com/yojiroo/MLHOL/blob/master/Azure%20Machine%20Learning(Node.js)/MLHOL_nodejs_img/7.png)
ML Studio へのサインイン
7.	ML Studio で、左下隅にある [+ NEW] をクリックします。[Blank Experiment] をクリックして、新しい実験を開始します。
![8](https://github.com/yojiroo/MLHOL/blob/master/Azure%20Machine%20Learning(Node.js)/MLHOL_nodejs_img/8.png)
空の実験の作成
8.	ページ上部にある実験のタイトル ("Experiment created on...") をクリックし、"Digit Recognition" などの新しい実験名を入力します。
![9](https://github.com/yojiroo/MLHOL/blob/master/Azure%20Machine%20Learning(Node.js)/MLHOL_nodejs_img/9.png)
これで、実験が作成されました。次のステップでは、データをインポートし、それに基づいてモデルを構築します。
# 演習 2: データセットをアップロードする
Azure Machine Learning Studio には、サンプルのデータセットがいくつか同梱されています。追加のデータセットは、Data.gov、Kaggle、カリフォルニア大学アーバイン校の Machine Learning Repository など、さまざまなソースから入手可能です。この演習では、43 名分の手書きの数字 (0 ～ 9) をスキャンしてデジタル化することにより生成したデータが含まれるパブリック データセットをアップロードします。後でこのデータセットを使用して、高い精度で手書きの数字を認識できる ML モデルのトレーニングを行います。
1.	ML Studio の左下隅にある [+ NEW] をクリックします。[DATASET]、[FROM LOCAL FILE] の順にクリックします。
![10](https://github.com/yojiroo/MLHOL/blob/master/Azure%20Machine%20Learning(Node.js)/MLHOL_nodejs_img/10.png)
データセットの新規作成
2.	[ファイルを選択] ボタンをクリックします。このラボの resources サブディレクトリに移動して、digit-recognition.csv という名前のファイルを選択します。[SELECT A TYPE FOR THE NEW DATASET] で "Generic CSV File with a header (.csv)" が選択されていることを確認します。オプションで、[ENTER A NAME FOR THE NEW DATASET] ボックスに、データセットのフレンドリ名を入力し、右下隅にあるチェック マークをクリックして、データセットのアップロードを開始します。
![11](https://github.com/yojiroo/MLHOL/blob/master/Azure%20Machine%20Learning(Node.js)/MLHOL_nodejs_img/11.png)
データセットのアップロード
3.	アップロードが完了するのを待ちます。左側にあるモジュール パレットに移動し、[Saved Datasets] の [My Datasets] の下に、アップロードしたデータセットが表示されていることを確認します.
![12](https://github.com/yojiroo/MLHOL/blob/master/Azure%20Machine%20Learning(Node.js)/MLHOL_nodejs_img/12.png)
Datasetsの確認画面
![13](https://github.com/yojiroo/MLHOL/blob/master/Azure%20Machine%20Learning(Node.js)/MLHOL_nodejs_img/13.png)
アップロードしたデータセット
6.	モジュール パレットからキャンバス (右側の灰色の領域) に、データセットをドラッグ アンド ドロップします。
![14](https://github.com/yojiroo/MLHOL/blob/master/Azure%20Machine%20Learning(Node.js)/MLHOL_nodejs_img/14.png)
モデルへのデータセットの追加
7.	データセットがどのようになっているかを確認するために、データセットの下部にある出力ポート (丸の中に 1 があるもの) をクリックして、[Visualize] を選択します。
![15](https://github.com/yojiroo/MLHOL/blob/master/Azure%20Machine%20Learning(Node.js)/MLHOL_nodejs_img/15.png)
データセットの視覚化
8.	データセットでは、変数、つまり "フィーチャー" は、列として表示され、各行が、0 ～ 9 の数字を表します。データセットには、3,823 行と 65 列が含まれています。最初の 64 列には、4 × 4 ブロックのピクセルを表す 0 ～ 16 の値が含まれています。データセットに表されている各数字は、32 × 32 配列にスキャンされたもので、1 つの数字あたり、合計 1,024 個のピクセルになります。データセットでは各スキャンは 64 個の値で表され、それぞれの値が 16 ピクセルを表します。
![16](https://github.com/yojiroo/MLHOL/blob/master/Azure%20Machine%20Learning(Node.js)/MLHOL_nodejs_img/16.png)
数字認識データセット
9.	"digit" という名前の最後の列が表示されるまで、右にスクロールします。この列内のそれぞれの値が、同じ行内のピクセル データに対応する数字です。これが、ほかの 64 列の値を考慮して、モデルによって予測されるターゲット値です。
![17](https://github.com/yojiroo/MLHOL/blob/master/Azure%20Machine%20Learning(Node.js)/MLHOL_nodejs_img/17.png)
ターゲットの変数
右上隅にある [x] をクリックして、視覚化ウィンドウを終了します。データがロードされます。これを使用して、次の演習を行います。
## 演習 3: 分類モデルのトレーニングを行う
この演習では、ML Studio のドラッグ アンド ドロップ ユーザー インターフェイスを使用して、ML モデルのトレーニングを行います。トレーニングでは、いずれかの機械学習アルゴリズムを選択して、モデルにデータを取り込みます。トレーニングの間に、コンピューターでは、今後の入力からの値を予測するときに使用できるデータのパターンが検索されます。
機械学習モデルには、いくつかの種類があります。最も一般的な種類の 1 つが、回帰モデルです。このモデルでは、多数ある回帰アルゴリズムの 1 つを使用して、数値を生成します。たとえば、人の年齢や、クレジットカード トランザクションが不正である確率などです。ここでは、分類モデルのトレーニングを行います。この種類のモデルでは、一連の入力を一連の既知の出力の 1 つに解決できるようにします。分類モデルの典型的な例として、電子メールを調査して、"スパム" または "非スパム" に分類する処理があります。ここでは、モデルを使用して、ピクセル パターンを表す一連の入力を調査し、それぞれを 0 ～ 9 までの数字に分類してみます。
1.	モジュール パレットの上部にある検索ボックスに「metadata」と入力して (かぎかっこは付けません)、[Edit Metadata] モジュールを検索します。
![18](https://github.com/yojiroo/MLHOL/blob/master/Azure%20Machine%20Learning(Node.js)/MLHOL_nodejs_img/18.png)
[Edit Metadata]モジュールの検索
2.	このモジュールを実験キャンバスにドラッグして、データセットの下にドロップします。次に、データセットの出力ポートから出ている下向き矢印をドラッグして、出力ポートを [Edit Metadata] モジュールの入力ポートに接続します。[Edit Metadata] モジュールを使用すると、特に、データセット内の列のデータの種類を指定できるようになります。
![19](https://github.com/yojiroo/MLHOL/blob/master/Azure%20Machine%20Learning(Node.js)/MLHOL_nodejs_img/19.png)
[Edit Metadata] モジュールの追加
Azure ML Studio で理解しておくべき主要概念の 1 つが、ポートとコネクタの概念です。このステップでは、データセットの出力ポートを [Edit Metadata] モジュールの入力ポートに接続しました。コネクタを介して、1 つのモジュールから次のモジュールにデータが流れます。モジュールによっては、複数の入力および出力をサポートするため、複数の入力ポートと出力ポートを持つものもあります。ポートの機能を知りたい場合は、ポートの上にカーソルを置くと、ヒントがポップアップ表示されます。より詳しい情報が必要な場合は、モジュールを右クリックして、表示されるポップアップ メニューから [Help] を選択します。
3.	[Edit Metadata] モジュールをクリックして、選択します (選択すると、ボールドの青い枠で囲まれます)。右側の [Properties] ペインにある [Launch column selector] ボタンをクリックします。
![20](https://github.com/yojiroo/MLHOL/blob/master/Azure%20Machine%20Learning(Node.js)/MLHOL_nodejs_img/20.png)
列セレクターの起動
4.	[AVAILABLE COLUMNS] の一覧の最下部までスクロールして、[digit] をクリックします。[>] ボタンをクリックして、[digit] を [SELECTED COLUMNS] の一覧に移動します。[digit] は、モデルが予測する値であることを思い出してください。ウィンドウの右下隅にあるチェック マークをクリックして、完了します。
![21](https://github.com/yojiroo/MLHOL/blob/master/Azure%20Machine%20Learning(Node.js)/MLHOL_nodejs_img/21.png)
[digit] 列の選択
5.	[Properties] ペインで、[Categorical] という名前のドロップダウン リストから [Make Categorical] を選択します。これにより、[digit] 列内の値を離散型 (7.5 や 8.6 などの値をとることができる数値に対立するもの) として扱うようモデルに指示されます。
![22](https://github.com/yojiroo/MLHOL/blob/master/Azure%20Machine%20Learning(Node.js)/MLHOL_nodejs_img/22.png)
[digit] 列をカテゴリ別として指定
6.	ページ下部の [SAVE] ボタンをクリックして、実験を保存します。
![23](https://github.com/yojiroo/MLHOL/blob/master/Azure%20Machine%20Learning(Node.js)/MLHOL_nodejs_img/23.png)
実験の保存
Azure Machine Learning Studio を使用して作業する場合、こまめに実験を保存するようにし、特にモデルを実行する前には忘れずに保存してください。そうすれば、問題が発生した場合でも、作業をやり直す必要がありません。また、先に実験を保存しないでブラウザーの [Back] ボタンをクリックすると、作業が失われる可能性があることにも注意してください。
7.	モジュール パレットの最上部にある検索ボックスに「split」 (かぎかっこは付けません) と入力します。キャンバスに [Split Data] モジュールをドラッグし、[Edit Metadata] モジュールの出力ポートを [Split Data] モジュールの入力ポートに接続します。[Split Data] モジュールの目的は、データセットを 2 つの部分に分割することで、1 つがトレーニング用、もう 1 つがスコア付け用です。[Split Data] モジュールは、トレーニング用とスコア付け用に個別のデータセットがない場合に有用です。
![24](https://github.com/yojiroo/MLHOL/blob/master/Azure%20Machine%20Learning(Node.js)/MLHOL_nodejs_img/24.png)
[Split Data] モジュールの追加
8.	[Split Data] モジュールが選択されていることを確認します。次に、右側の [Properties] ペインに移動して、分割を 0.8 に設定します。行の 80% をモデルのトレーニングに使用し、残りの 20% をモデルのスコア付けに使用します。[Randomized split] チェック ボックスがオンになっていることも確認します。これは、特に、順序付けされているデータを分割するときに重要です。
![25](https://github.com/yojiroo/MLHOL/blob/master/Azure%20Machine%20Learning(Node.js)/MLHOL_nodejs_img/25.png)
分割の割合の設定
9.	ページ下部の [SAVE] ボタンをクリックして、実験を保存します。
10.	ページ下部の [RUN] ボタンをクリックして、実験を実行します。
11.	次に、モジュール パレットの最上部にある検索ボックスに「train model」 (かぎかっこは付けません) と入力します。[Train Model] モジュールを見つけて、キャンバスにドラッグします。[Split Data] モジュールの左の出力を [Train Model] モジュールの右の入力に接続します。
![26](https://github.com/yojiroo/MLHOL/blob/master/Azure%20Machine%20Learning(Node.js)/MLHOL_nodejs_img/26.png)
[Train Model] モジュールの追加
12.	次のステップはきわめて重要です。このステップでは、[Train Model] モジュールで予測することになる値を指定します。[Properties] ペインで [Train Model] モジュールを選択して、[Launch column selector] をクリックします。
![27](https://github.com/yojiroo/MLHOL/blob/master/Azure%20Machine%20Learning(Node.js)/MLHOL_nodejs_img/27.png)
列セレクターの起動
13.	「digit」 (かぎかっこは付けません) と入力して、[digit] 列を選択します。これが、モデルが予測することになる値です。右下隅にあるチェック マークをクリックして、完了します。
![28](https://github.com/yojiroo/MLHOL/blob/master/Azure%20Machine%20Learning(Node.js)/MLHOL_nodejs_img/28.png)
ターゲット値の指定
14.	次に、学習アルゴリズムを選択します。Azure Machine Learning では、数種類の分類アルゴリズムと回帰アルゴリズムが提供されており、そこから選択することができます。また、他の種類のアルゴリズムも提供されています。それぞれのアルゴリズムが、モジュール パレットでモジュールとして表されます。いつでも、R または Python で独自のアルゴリズムをコーディングすることもできます。ここでは、モデルに "Multiclass Logistic Regression" として知られているアルゴリズムを採用します。これは、分類アルゴリズムの 1 つで、結果の確率を予測するために統計でよく使用されているロジスティック回帰が適用されます。
Azure Machine Learning チームは、お客様がモデルの意図した目的に基づいて、どのアルゴリズムを選択すべきか判断できるように、"チート シート" を作成しています。チート シートは、こちらからダウンロードできます。
モジュール パレットの最上部にある検索ボックスに「multiclass」 (かぎかっこは付けません) と入力します。[Multiclass Logistic Regression] モジュールを見つけて、キャンバスにドラッグします。次に示しているように、[Multiclass Logistic Regression] モジュールの出力を [Train Model] モジュールの左の入力に接続します。
![29](https://github.com/yojiroo/MLHOL/blob/master/Azure%20Machine%20Learning(Node.js)/MLHOL_nodejs_img/29.png)
学習アルゴリズムの指定
15.	[SAVE] ボタンをクリックして、実験を保存します。
16.	[RUN] ボタンをクリックして、モデルを実行します。すべてのモジュールに、正常に実行されたことを示す緑色のチェック マークが表示されるまで待ちます。
17.	出力の計算における入力変数それぞれの影響の大きさを確認する場合は、[Train Model] モジュールの出力ポートを右クリックして、[Visualize] を選択します。モデルのフィーチャー (列) と、それに割り当てられている重みの一覧が表示されます。
これでモデルにトレーニングを行いました。ただし、値の予測において、モデルはどれほど正確なのでしょうか。これが、スコア付けを行う理由です。
演習 4: モデルにスコアを付ける
この演習では、前の演習でトレーニングを行ったモデルにスコアを付けます。"スコア付け" によって、モデルのトレーニングがどれほどうまく行われたかが判断されます。つまり、モデルが、提示された変数からターゲット値をどれほど正確に予測するかということです。ML Studio では、スコア付けのプロセスがきわめて簡単になります。アップロードしたデータセットの 80% をモデルのトレーニングに使用しました。残りの 20% をモデルのスコア付けに使用します。
1.	キャンバスに [Score Model] モジュールを追加します。[Train Model] モジュールの出力を [Score Model] モジュールの左の入力ポートに接続します。次に、[Split Data] モジュールの右の出力ポートを [Score Model] モジュールの右の入力に接続します。この接続は、トレーニングには使用されなかった 20% のデータを表します。
![33](https://github.com/yojiroo/MLHOL/blob/master/Azure%20Machine%20Learning(Node.js)/MLHOL_nodejs_img/33.png)
[Score Model] モジュールの追加
2.	[SAVE] ボタンをクリックして、実験を保存します。
3.	[RUN] ボタンをクリックして、実験を実行します。
4.	実行が完了したら、[Score Model] モジュールの出力ポートを右クリックして、[Visualize] を選択します。右にスクロールします。[digit] というラベルが付いている列に、[Score Model] モジュールに渡された 20% のデータセットの実際の値が表示されています。横の、[Scored Probabilities for Class X] という名前の 10 列は、モデルが左の入力値を使用して、可能な 10 個の出力値 (数字 0 ～ 9) をどのようにスコア付けしたかを示しています。数値が高い方が、一致の可能性が高くなります。最終列の [Scored Labels] は、入力値からモデルが予測した数字を示しており、これは、最も高い確率であるとスコア付けされた数字です。[digit] 列の値と [Scored Labels] の値とを比較することによって、モデルの正確性を評価できます。簡単な検査により、モデルがほとんど常に正しく機能していることが明らかになります。
![34](https://github.com/yojiroo/MLHOL/blob/master/Azure%20Machine%20Learning(Node.js)/MLHOL_nodejs_img/34.png)
スコア付けしたデータセット
5.	右上隅にある [x] をクリックして、視覚化ウィンドウを終了します。
6.	数百もの値を手動で比較する必要はなく、結果の品質を評価するには、[Evaluate Model] モジュールを実験キャンバスに追加します。[Score Model] モジュールの出力ポートを [Evaluate Model] モジュールの左の入力ポートに接続します ([Evaluate Model] は 2 つのモデルの比較に使用することがあるため、入力ポートが 2 つあります)。
![35](https://github.com/yojiroo/MLHOL/blob/master/Azure%20Machine%20Learning(Node.js)/MLHOL_nodejs_img/35.png)
[Evaluate Model] モジュールの追加
7.	[Save] をクリックして、実験を保存します。[RUN] ボタンをクリックして、もう一度実験を実行します。
8.	[Evaluate Model] モジュールの出力ポートをクリックして、メニューから [Visualize] を選択します。[Overall accuracy] および [Average accuracy] の数値から、モデルがうまく機能していることがわかります。手書きの数字のデジタル化スキャンの場合、97% を超える高い確率で、数字を正しく識別できます。
![36](https://github.com/yojiroo/MLHOL/blob/master/Azure%20Machine%20Learning(Node.js)/MLHOL_nodejs_img/36.png)
評価の結果
9.	右上隅にある [x] をクリックして、視覚化ウィンドウを終了します。
これで、モデルの構築とテストが完了し、次は実行に移ります。最終的な目標は、PC またはモバイル デバイスで動作し、クラウドまでつながって、モデルを実行するアプリを作成することです。これを簡単にするには、モデルを Web サービスとして展開する必要があります。
演習 5: モデルを Web サービスとして展開する
モデルのトレーニングとスコア付けが完了したら、そのモデルを Web サービスとして展開して、プログラムによって操作することができます。Web サービスとして展開する前に、実験を効率化する必要があります。これには、トレーニングを行ったモデルからの新しい実験の作成、不要なモジュールの削除、および Web サービスの入力および出力モジュールの追加が含まれます。幸運にも、ML Studio がこれらすべての処理を実行してくれます。
1.	[RUN] ボタンをクリックして、モデルをもう一度実行します。
2.	画面の最下部にある [SET UP WEB SERVICE] ボタンをクリックして、表示されるメニューで [Predictive Web Service [Recommended]] を選択します。このオプションが灰色表示になっている場合は、[RUN] ボタンをクリックして再試行します。
![37](https://github.com/yojiroo/MLHOL/blob/master/Azure%20Machine%20Learning(Node.js)/MLHOL_nodejs_img/37.png)
予測 Web サービスの作成
3.	ML Studio が数秒間処理を行い、その後に予測実験が表示されます。[Split Data] と [Train Model] がなくなって、そのすべてのトレーニング データが [Score Model] に流れています。さらに、最上部と最下部に、Web サービスの入力と出力が追加されています。
元のモデルが消えてしまったと心配する必要はありません。元のモデルは残っています。ページの最上部にある [Training experiment] タブをクリックすると、元のモデルを表示できます。
![38](https://github.com/yojiroo/MLHOL/blob/master/Azure%20Machine%20Learning(Node.js)/MLHOL_nodejs_img/38.png)
予測実験
4.	予測分析を実行するために呼び出すことができる Web サービスを作成するには、[RUN] をもう一度クリックします。処理が完了したら、[DEPLOY WEB SERVICE] ボタンをクリックして、Web サービスを展開します。
![39](https://github.com/yojiroo/MLHOL/blob/master/Azure%20Machine%20Learning(Node.js)/MLHOL_nodejs_img/39.png)
Web サービスの展開
5.	すぐに、新しい Web サービスのダッシュボードが表示されます。このダッシュボードには、Web サービスをテストするためにクリックできる [Test] ボタンが含まれています (ここでは、ピクセル パターンを表す 0 ～ 16 の 64 個の値を入力する必要があるため、あまり実用的ではありません)。また、アプリでサービスに対する認証済み呼び出しを行うときに使用できる API キーも含まれています。この API キーは、次の演習で使用します。
便宜上、API の右にあるボタンをクリックして、キーをクリップボードにコピーし、テキスト エディターに貼り付けておくと、後で簡単に取得できます。
![40](https://github.com/yojiroo/MLHOL/blob/master/Azure%20Machine%20Learning(Node.js)/MLHOL_nodejs_img/40.png)
API キーのコピー
6.	[Consume] をクリックして、REST を使用した HTTPS 経由での Web サービスの呼び出しに関する有用な情報を表示します。
![41](https://github.com/yojiroo/MLHOL/blob/master/Azure%20Machine%20Learning(Node.js)/MLHOL_nodejs_img/41.png)
[Request/Response] の情報の表示
7.	このページには、以下のような、Web サービスを利用するアプリを作成する必要のあるプログラマーに有用なドキュメントが提供されます。
o	HTTPS 要求のフォーマットの方法
o	HTTPS 応答のフォーマットの方法
o	入力および出力パラメーター
o	Web サービスを呼び出す方法を示したサンプル コード
ページの最下部までスクロールすると、サンプル コードが表示されています。これは、C#、Python、および R の 3 つの言語で利用できます。[C#] を選択して、Web サービスの URL を client.BaseAddress に割り当てるステートメントを確認できるまで、下にスクロールします。次の演習で取得できるように、この URL をクリップボードにコピーして、テキスト エディターに貼り付けておきます。後で、Web サービスを呼び出すために、これと同様のコードを記述します。そのために、この URL が必要になります。
![42](https://github.com/yojiroo/MLHOL/blob/master/Azure%20Machine%20Learning(Node.js)/MLHOL_nodejs_img/42.png)
Web サービスの URL
Azure ML Web サービスに関してよく聞かれるのが、コストはいくらかという質問です。現在の価格情報については、「Machine Learning の価格」ページを参照してください。

# 演習 6: ユニバーサル Windows クライアントを構築する
Azure ML モデルを Web サービスとして展開する理由は、そうすることによってモデルを利用するスマート アプリを構築できることにあります。そのようなアプリを構築する方法はいろいろあります。たとえば、JavaScript や AJAX を使用して Web アプリから Web サービスを呼び出すことができます。あるいは、Visual Studio を使用して、iOS、Android、または Windows で動作し、.NET の HttpClient クラスを使用して Web サービスを呼び出す Xamarin アプリを作成することもできます。
この演習では、ユニバーサル Windows プラットフォーム (UWP) を対象とするクライアント アプリを作成します。このアプリの利点は、PC、タブレット、スマートフォン、そして Xbox One まで含めた、さまざまな Windows デバイスで動作することです。作成したアプリを使用すると、画面上のグリッド内に数字を描画できるようになります。そうすれば、アプリによって ML Web サービスが呼び出され、描画した数字が伝えられます。
1.	Windows 10 PC で UMP アプリを構築して実行するには、デバイスで開発者モードを有効にする必要があります。開発者モードが有効になっていることを確認するには、デスクトップの左下隅にある Windows ボタン (スタート ボタンともいいます) をクリックします。メニューから [Settings] を選択して、[Settings] ダイアログで [Update & security] をクリックします。以下に示すように、左側の [For developers] をクリックして、右側の [Developer mode] をクリックします。
![1](https://github.com/yojiroo/MLHOL/blob/master/Azure%20Machine%20Learning%EF%BC%88UWP%EF%BC%89/MLHOL_UWP_img/1.png)
Windows 10 での開発者モードの有効化
2.	Visual Studio 2015 を起動して、[File]、[New]、[Project] コマンドの順に進み、"MLClient" という名前の新しい空のアプリケーション (ユニバーサル Windows) プロジェクトを作成します。
![2](https://github.com/yojiroo/MLHOL/blob/master/Azure%20Machine%20Learning%EF%BC%88UWP%EF%BC%89/MLHOL_UWP_img/2.png)
新しい UWP プロジェクトの作成
3.	[OK] をクリックして、既定のターゲット バージョンと最小バージョンを受け入れます。
![3](https://github.com/yojiroo/MLHOL/blob/master/Azure%20Machine%20Learning%EF%BC%88UWP%EF%BC%89/MLHOL_UWP_img/3.png)
UWP のバージョンの選択
4.	[Solution Explorer] ウィンドウで、MLClient プロジェクトを右クリックし、[Manage NuGet Packages...] を選択します。
![4](https://github.com/yojiroo/MLHOL/blob/master/Azure%20Machine%20Learning%EF%BC%88UWP%EF%BC%89/MLHOL_UWP_img/4.png)
プロジェクトの NuGet パッケージの管理
NuGet は、Microsoft 管理プラットフォーム向けの無料のオープンソース パッケージです。NuGet では、さまざまなタスクを実行するコードが収録された数千ものライブラリ、つまりパッケージを利用できます。NuGet は Visual Studio 2015 に統合されているため、簡単に NuGet をプロジェクトに追加して、コードを記述せずに多くのタスクを実行できます。
5.	[Browse] をクリックします。検索ボックスに、「webapi.client」と入力します (かぎかっこは付けません)。Microsoft.AspNet.WebApi.Client をクリックして、NuGet からこの Web API クライアント パッケージを選択します。最後に、[Install] をクリックして、最新の安定バージョンのパッケージをインストールします。このパッケージには、アプリで Web サービスとの通信に使用する ヘルパー API が収録されています。変更の確認を求められたら [OK] をクリックし、ダウンロードしたパッケージのライセンスへの同意を求められたら [I Accept] をクリックします。
![5](https://github.com/yojiroo/MLHOL/blob/master/Azure%20Machine%20Learning%EF%BC%88UWP%EF%BC%89/MLHOL_UWP_img/5.png)
Web API クライアントのインストール
6.	[Solution Explorer] ウィンドウで、MLClient プロジェクトを右クリックし、[Add]、[Class] の順に選択して、プロジェクトにクラスを追加します。表示されるダイアログで、[Name] ボックスに「StringTable.cs」と入力して (かぎかっこは付けません)、[Add] をクリックします。
![6](https://github.com/yojiroo/MLHOL/blob/master/Azure%20Machine%20Learning%EF%BC%88UWP%EF%BC%89/MLHOL_UWP_img/6.png)
StringTable クラスの追加
以下のように StringTable クラスを実装します。
```
class StringTable
{
    public string[] ColumnNames { get; set; }
    public string[,] Values { get; set; }
}
```
MainPage.xaml を開き、以下に示した図で強調表示されている空の Grid 要素を見つけます。
![7](https://github.com/yojiroo/MLHOL/blob/master/Azure%20Machine%20Learning%EF%BC%88UWP%EF%BC%89/MLHOL_UWP_img/7.png)
空の Grid 要素
空の Grid を次のマークアップに置き換えます。
```
<StackPanel VerticalAlignment="Center">
    <TextBlock Text="Draw a digit from 0 to 9" FontSize="28" Margin="0,0,0,8" HorizontalAlignment="Center" />
    <Grid x:Name="Cells" Width="400" Height="400">
        <!-- Empty cell grid filled programmatically -->
    </Grid>
    <Button Content="Submit" Width="400" Height="100" FontSize="32" Margin="0,16,0,0" HorizontalAlignment="Center" Click="OnSubmit" />
    <Button Content="Clear" Width="400" Height="100" FontSize="32" Margin="0,16,0,0" HorizontalAlignment="Center" Click="OnClear" />
</StackPanel>
```
挿入したマークアップは、Extensible Application Markup Language (XAML) です。XAML は、ユーザー インターフェイスの構築用にマイクロソフトが作成した言語です。当初は WPF に向けて作成されましたが、その後ユニバーサル Windows アプリ向けに用途変更されています。XAML を Xamarin Forms と組み合わせると、iOS や Android 向けのユーザー インターフェイスの構築にも使用できます。XAML は、Visual Studio やその他のよく使用されているツールでデザイナー サポートを提供する、表現がきわめて豊富な言語です。
7.	次に、MainPage.xaml.cs を開き、ページの上部に既にある using ステートメントに、以下の using ステートメントを追加します。
```
using Windows.UI.Xaml.Shapes;
using Windows.UI;
using Windows.Devices.Input;
using System.Net.Http;
using System.Net.Http.Headers;
using System.Threading.Tasks;
using Windows.UI.Core;
using Windows.UI.Popups;
using Newtonsoft.Json;
```
続けて MainPage.xaml.cs で、MainPage クラス内のすべてのコードを以下のコードに置き換えます。
```
private const double _margin = 2.0;  // Margin around each cell
private const double _opacity = 0.2; // Opacity of empty cells
private Rectangle _last;

public MainPage()
{
    this.InitializeComponent();

    // Add rows and columns to the Grid
    for (int i = 0; i < 8; i++)
        Cells.ColumnDefinitions.Add(new ColumnDefinition());

    for (int j = 0; j < 8; j++)
        Cells.RowDefinitions.Add(new RowDefinition());

    // Fill the Grid with Rectangles
    for (int row = 0; row < 8; row++)
    {
        for (int col = 0; col < 8; col++)
        {
            var cell = new Rectangle();
            cell.Fill = new SolidColorBrush(Colors.Blue);
            cell.Opacity = _opacity;
            cell.Margin = new Thickness(_margin);
            cell.SetValue(Grid.RowProperty, row);
            cell.SetValue(Grid.ColumnProperty, col);
            cell.PointerPressed += OnCellPressed;
            cell.PointerEntered += OnCellEntered;
            Cells.Children.Add(cell);
        }
    }
}

private void OnCellPressed(object sender, PointerRoutedEventArgs e)
{
    if (e.Pointer.PointerDeviceType == PointerDeviceType.Mouse)
    {
        var point = e.GetCurrentPoint(null);

        if (point.Properties.IsLeftButtonPressed)
        {
            var cell = (Rectangle)sender;
            ToggleCell(cell); // Toggle the cell (left mouse button only)
            _last = cell;
        }
    }
}

private void OnCellEntered(object sender, PointerRoutedEventArgs e)
{
    var cell = (Rectangle)sender;

    if (e.Pointer.PointerDeviceType == PointerDeviceType.Mouse)
    {
        var point = e.GetCurrentPoint(null);

        if (!point.Properties.IsLeftButtonPressed)
            return; // Ignore if it's a mouse but not the left button

        if (cell == _last)
        {
            _last = null;
            return; // Ignore if it's a mouse but the cell was toggled in OnCellPressed
        }
    }

    ToggleCell(cell);
}

private void ToggleCell(Rectangle cell)
{
    cell.Opacity = (cell.Opacity < 1.0) ? 1.0 : _opacity;
}

private async void OnSubmit(object sender, RoutedEventArgs e)
{
    // Package up the data
    string[] values = new string[65];

    for (int row = 0; row < 8; row++)
    {
        for (int col = 0; col < 8; col++)
        {
            int index = (row * 8) + col;
            values[index] ((Rectangle)Cells.Children[index]).Opacity == 1.0 ? "16" : "0";
        }
    }

    values[64] = "0"; // digit parameter

    try
    {
        // Call the ML service
        await MLSubmitAsync(values);
    }
    catch(Exception ex)
    {
        // Let the user know if something went wrong
        var dialog = new MessageDialog(ex.Message);
        await dialog.ShowAsync();
    }
}

private void OnClear(object sender, RoutedEventArgs e)
{
    for (int i = 0; i < 64; i++)
        ((Rectangle)Cells.Children[i]).Opacity = _opacity;
}

private async Task MLSubmitAsync(string[] v)
{
    using (var client = new HttpClient())
    {
        var request = new
        {
            Inputs = new Dictionary<string, StringTable>()
            {
                {
                    "input1",
                    new StringTable()
                    {
                        ColumnNames = new string[]
                        {
                            "p01", "p02", "p03", "p04", "p05", "p06", "p07", "p08",
                            "p09", "p10", "p11", "p12", "p13", "p14", "p15", "p16",
                            "p17", "p18", "p19", "p20", "p21", "p22", "p23", "p24",
                            "p25", "p26", "p27", "p28", "p29", "p30", "p31", "p32",
                            "p33", "p34", "p35", "p36", "p37", "p38", "p39", "p40",
                            "p41", "p42", "p43", "p44", "p45", "p46", "p47", "p48",
                            "p49", "p50", "p51", "p52", "p53", "p54", "p55", "p56",
                            "p57", "p58", "p59", "p60", "p61", "p62", "p63", "p64",
                            "digit"
                        },
                        Values = new string[,]
                        {
                            {
                                v[0],  v[1],  v[2],  v[3],  v[4],  v[5],  v[6],  v[7],
                                v[8],  v[9],  v[10], v[11], v[12], v[13], v[14], v[15],
                                v[16], v[17], v[18], v[19], v[20], v[21], v[23], v[23],
                                v[24], v[25], v[26], v[27], v[28], v[29], v[30], v[31],
                                v[32], v[33], v[34], v[35], v[36], v[37], v[38], v[39],
                                v[40], v[41], v[42], v[43], v[44], v[45], v[46], v[47],
                                v[48], v[49], v[50], v[51], v[52], v[53], v[54], v[55],
                                v[56], v[57], v[58], v[59], v[60], v[61], v[62], v[63],
                                v[64]
                            }
                        }
                    }
                },
            },
            GlobalParameters = new Dictionary<string, string>() { }
        };

        const string key = "api_key";
        client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", key);
        client.BaseAddress = new Uri("web_service_url");

        HttpResponseMessage response = await client.PostAsJsonAsync("", request).ConfigureAwait(false);

        // Resumes on background thread, so marshal to the UI thread
        await this.Dispatcher.RunAsync(CoreDispatcherPriority.Normal, async () =>
        {
            if (response.IsSuccessStatusCode)
            {
                string json = await response.Content.ReadAsStringAsync();
                dynamic result = JsonConvert.DeserializeObject<dynamic>(json);
                var digit = result.Results.output1.value.Values[0][75];
                var dialog = new MessageDialog(String.Format("Azure ML says you entered a {0}", digit));
                await dialog.ShowAsync();
            }
            else
            {
                var dialog = new MessageDialog(String.Format("The request failed with status code: {0}", response.StatusCode));
                await dialog.ShowAsync();
            }
        });
   }
}

```
これは大量のコードですね。しかし、大きな効果もあります。より詳細に把握するには、このコードの以下の点について、留意しておくとよいです。
o	MainPage のクラス コンストラクターによって、MainPage.xaml で宣言された Grid 要素に XAML の Rectangle が追加され、8 × 8 のグリッドが形成されます。このような XAML 要素は通常、宣言により作成されますが、ここでは、コード内に XAML 要素を作成することによって、MainPage.xaml に、Rectangle 要素とほぼ同じ行が次々と追加されないようにしています。
o	OnCellEntered は、指、ペン、またはマウス (UWP の用語では、ポインティング デバイス) が 64 個の Rectangle のいずれかに接触したときに呼び出されるメソッドです。OnCellEntered のジョブは、Rectangle の不透明度を変更することによる、"オン" と "オフ" の切り替えです。
o	OnSubmit メソッドは、[Submit] ボタンをクリックしたときに呼び出されます。OnSubmit メソッドは、8 × 8 グリッドをスキャンして、どのスクエアが "オン" であるか判別し、データを MLSubmit に渡します。
o	MLSubmitAsync は、精巧に機能します。MLSubmitAsync は、UWP の HttpClient クラスを使用して、Web サービスに対して REST 呼び出しを実行します。MLSubmitAsync は、Web サービスのダッシュボードに表示される C# のサンプル コードの後で、厳密にパターン化されています。
8.	挿入したソース コードで、api_key を、演習 5 のステップ 5 で取得した Web サービス用の API キーに置き換えます。
9.	次に、web_service_url を、演習 5 のステップ 7 で取得した Web サービスの URL に置き換えます。
10.	[Visual Studio] ウィンドウの上部にある [Build] メニューに移動し、[Build Solution] コマンドを使用して、ソリューションをビルドします。報告されたビルド エラーがある場合はそれを修正してから、Ctrl + F5 キーを押して、アプリを起動します。次のように表示されることを確認します。
![8](https://github.com/yojiroo/MLHOL/blob/master/Azure%20Machine%20Learning%EF%BC%88UWP%EF%BC%89/MLHOL_UWP_img/8.png)
MLClient アプリ
おつかれさまでした。以上で、Azure Machine Learning を利用してインテリジェンスを実現するユニバーサル Windows アプリの完成です。最後のタスクでは、アプリケーションを実際に実行して、高度な処理能力を確認します。
演習 7: モデルをテストする
MLClient によって、Web サービスの呼び出しにグラフィカル フロントエンドが追加されます。グリッドのスクエアの上に指、ペン、またはマウスをドラッグすることによって、グリッド内に数字を描画します。[Submit] ボタンをクリックすると、64 個の値の配列が作成され (1 つのスクエアに 1 つの値、トレーニング データベースの 64 のフィーチャー列それぞれに 1 つの値)、その配列が JSON にシリアル化されて、Web サービスに渡されます。戻された JSON がアプリによって逆シリアル化され、結果が表示されます。基本的に MLClient クライアントは、ML モデルの正確性を測るための高度に視覚的な方法を提供します。
1.	以下の図のように、マウスを使用してグリッドに「7」と描画します。その後、[Submit] ボタンをクリックします。
![9](https://github.com/yojiroo/MLHOL/blob/master/Azure%20Machine%20Learning%EF%BC%88UWP%EF%BC%89/MLHOL_UWP_img/9.png)
アプリのテスト
2.	すぐに、ポップアップ ウィンドウが表示され、描画した数字が示されます。数字は合っていますか。
![10](https://github.com/yojiroo/MLHOL/blob/master/Azure%20Machine%20Learning%EF%BC%88UWP%EF%BC%89/MLHOL_UWP_img/10.png)
3.	[Clear] ボタンをクリックして、グリッドをクリアし、ほかの数字をいくつか試してみます。数字によって、構築したモデルでうまく識別されるものとそうでないものがあり、描かれた数字がグリッドを埋め尽くす面積が大きいほど結果がよいことがわかるでしょう。
間違った答えは、一部にはモデルのトレーニングに使用したデータセットが比較的小規模であった結果というものもあれば (データセットは約 0.5 MB でしたが、ビッグデータの標準としては、小規模です)、画面上のグリッドの MLClient の表示では、モデルのトレーニングに使用したスキャンの解像度の 1/16 しか使用されないという事実によるものもあります。それでもなお、アプリでこのような方法で基本的な OCR を実行できるというのは、快挙ともいえるでしょう。また、手間のかかる作業を Azure Machine Learning に肩代わりさせることでこのようなアプリを構築できるということもおわかりいただけると思います。
4.	ユニバーサル Windows プラットフォームの利点の 1 つが、アプリをさまざまなデバイスで実行できることであるのを思い出してください。MLClient がタブレットでどのように表示されるかを確認するには、メニュー バーの下のデバイスのドロップダウン リストから [Simulator] を選択します。
![11](https://github.com/yojiroo/MLHOL/blob/master/Azure%20Machine%20Learning%EF%BC%88UWP%EF%BC%89/MLHOL_UWP_img/11.png)
ローカル コンピューターから Windows Simulator への切り替え
5.	Ctrl + F5 を押して、シミュレーターでアプリを起動します。
![12](https://github.com/yojiroo/MLHOL/blob/master/Azure%20Machine%20Learning%EF%BC%88UWP%EF%BC%89/MLHOL_UWP_img/12.png)
タブレットでの MLClient
通常、開発者は、異なるデバイスで UWP アプリを表示する方法を最適化するために追加の作業を少し行います。この作業については、このラボの範囲を超えていますが、作業やビジネスで UWP アプリを使用する計画がある場合は、認識しておくべき点でもあります。広範囲にわたるデバイスで UMP アプリの表示を最適にするための適応型レイアウト使用の詳細については、このビデオをご覧ください。
まとめ
このハンズオン ラボでは、以下の方法について学習しました。
+	Azure Machine Learning の実験を作成する
+	データセットをアップロードする
+	Azure Machine Learning モデルのトレーニングとスコア付けを行う
+	モデルを Web サービスとして展開する
+	REST を使用して Web サービスを呼び出す
Azure Machine Learning でできることはほかにもたくさんあり、今回学習した内容は出発点にすぎません。Azure Machine Learning を使ってあれこれ試しながら、生産的であるだけでなく、楽しくもあるツールで予測分析のわくわくする世界を探索してください。
________________________________________
Copyright 2016 Microsoft Corporation. All rights reserved. 特に注記がない限り、これらの資料は MIT ライセンスの使用条件下でライセンス供与されます。これらの資料は、MIT ライセンスに従い、プロジェクトに最も適切と判断された場合は使用することができます。このライセンスの使用条件については、https://opensource.org/licenses/MIT を参照してください。
