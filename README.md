# SerialEM SPA scritps for CRYO ARM series

## 前置き
このスクリプトはJEOL製CRYO ARMシリーズ(200,300,300Ⅱ)の単粒子解析用データ収集です。他の装置で動作するかどうかわかりません。
このスクリプトをインストールしたい場合は以下の手順を試してみてください。もし、わからない場合は日本電子のアプリケーションエンジニアか私に相談してみてください。

### 日本電子の装置を使用した単粒子解析の考え方
・日本電子の装置ではステージの精度がそれほど高くありません。そのため、SerialEM従来のやり方では正確に場所をアライメントするために色々な工夫、もしくはアライメントを正確に行うための時間が必要になってしまいます。そこで、正確性を少し犠牲にして速さを追求したのが、私たちが提供するスクリプトです。数スクエアのスクリーニングが30-60分、その後データ収集に+30-60分が目標です。大まかな流れをだけをここに示します。

また、スクリーニングとデータ収集を行っている様子のvideoを用意します。
詳しいマニュアルは筑波大学の岩崎ラボ、KEK安達のリンクを参照にして下さい。


## 事前準備、用意しておくこと
・SerialEMのキャリブレーションとインストール(ver4以上)が完了していること

・Focus/Tuneメニュー  Drift Protection にチェックが入っていること

・PyJEMプログラム群のSetCLaperture.bat/py, SetOLaperture.bat/py, shift_FL_client.bat/py, shift_FL_server.bat/pyをスクリプト40: EMproperties内に書いてあるWORKING_DIR_PATH のフォルダ+Tool(DefaultのWORKING_DIR_PATHはC:\ProgramData\SerialEM, つまりC:\ProgramData\SerialEM\Tool)に入れること

・上で入れたPyJEMプログラムは単体(batファイル)で動くか確認すること(minicondaをactivateするかどうかなど、環境次第なのでこれはアプリケーション技術員が対応)
確認方法：それぞれのプログラムをコマンドプロンプト上で単体で動かしてチェックする。まず、cmdコンソールを開き、WORKING_DIR_PATHのフォルダを開いて、cmdコンソールにドラッグ・アンド・ドロップする。引数は以下を参考にする。
→もしくはフォルダを開いた後Shift+右クリックでOpen command window hereを選択する。
例)SetCLAperture.batの場合： SetCLaperture.bat 0 1 
 1番のCL apertureがセットされる。(引数1は0で1段目のアパチャ、1で2段目のアパチャ、引数2は1-4番目のアパチャのset)
例)SetOLAperture.batの場合： SetOLaperture.bat 2 1　
 1番のOL apertureがセットされる。(引数は0でアパチャの取り出し、1-4でそれに相当するアパチャのset)
→プログラムは動くが、いずれの引数でもlistにないというエラーが出る場合は引数を確認。
shift_FL_client.bat, shift_FL_server.batについてはスクリプトZLPalignByFLで確認できる（後述）

・デフォルトのホールテンプレート(よく使うホールの大きさで作成しておく)を準備し、C:¥ProgramData¥SerialEM¥Data¥HoleImage¥hole_template.mrc
に事前にコピーしておくこと

## 新しいスクリプトのインストール方法
新しいスクリプトをダウンロード後、任意の場所にコピー(例：C:¥ProgramData¥SerialEM¥)
メニューscript  Load new packageから先程コピーしたスクリプトをロードする。

### EMProperties の値を確認および入力
　もし以前同様のスクリプトを使用していた場合は下記の値をそちらから値をコピーすると簡単で早い（変数名が変更してある可能性があるので注意が必要である）。
<JEOL TEM type>
・CLapt_type = 1 : # 0: CryoARM200, CryoARM200CA, CryoARM300II, F200
                 # 1: 2nd CL aperture board, CryoARM300
・ARM300：CRYO ARM 300(Z300)の場合は1にする

<Stage setting>
・safty_z_lower = -210 #[um], safty_z_upper = 205 #[um] :zの限界値
・eucentric_height = 0 # [um] ：0がデフォルト
・koehler_z_offset = -140 # [um] ：0がデフォルト、ケラー使う場合は-120程度入れるとよい。その場合はTakeSquare時にadjust_eucentric = 1にしないとケラーの高さにいかない。

<Setting for Atlas>
・default_spot_atlas: Atlas map作成時のspotサイズ
・mag_atlas: Atlas map作成時の倍率(CRYO ARM 300なら50,  CRYO ARM 200なら30)
・brightness_atlas: 100%が多く使われる。
・overlap_x, overlap_y, frame_x, frame_y：これらの値はnavigatorのfull montageから取得
・Full size of atlas: 合計何枚撮影するか。
        fullFrameX = 3
        fullFrameY = 4
・FOV offset from Atlas to Square
        atlas2square_x = -70 # [um]    
        atlas2square_y = 116.5 # [um]   
: AtlasからSquareに移行したときのshift to markerの値、もしアップデートしたい場合はそれらの値に足し算して記述すること

<Settingfor Square>
・default_SquareMag = 150：squaremapを取得するときの倍率
・spot_square = 4：squaremapを取得するときのspot size
・brightness_square = 100：100%が多く使われる。ただしenergy filterを入れる場合はその限りではなく、ちょうどよい明るさが存在する。FL focusと合わせて調整するとよい。
・FOV offset From Square to View
        square2view_x = 20 # [um]  
        square2view_y = 30 # [um]  
: SquareからViewに移行したときのshift to markerの値、もしアップデートしたい場合はそれらの値に足し算して記述すること

<Python Call setting>
各プログラムの場所を確認

<YoneoLocker>
・progdir = C:\Users\VALUEDGATANCUSTOMER\Desktop\yoneoLocr-main：Hole alignにyoneoLocrを使用する場合のプログラムの場所。

他にも沢山の値が存在するが必要最低限なものは以上。


スクリプトについての注意点
＊EMPropertiesの値は普段変更を加えないもの(FOV offset from Atlas to Square, FOV offset From Square to Viewは１週間に一度程度の変更)。
＊スクリプト内のparameterはグリッドや条件によって変更しやすいもの




### 確認すべきスクリプト：
** 1. TakeAtlas **
parameter: Clapt_before, Clapt_afterの値などをチェック(300は0 (open), 3300/200は1(150um))
はじめは、shift_atlas2square=0, goto_Square=1として、AtlasとSquare間の両倍率のField Of View (FOV)をあわせてどの程度ずれたかを確認する。この値をproperties(atlas2square_x, atlas2square_y)に入れる。shift_atlas2square=1とすると次回以降この値が適用されAtlasとSquare間の両倍率の移動が楽になる。
How to do：TakeAtlas後にマップからターゲットを選びadd pointsおよびgoto xyでステージ移動する。その後Squareの倍率に行き、ターゲットをその倍率で真ん中に来るようにステージ移動し撮影する。ターゲットをクリックしたあとにshift to makerで両倍率のFOVをあわせてどれ程度ずれたかを確認する。pop upで出てきたこの値をproperties(atlas2square_x, atlas2square_y)に入れるとよい。

** 2. TakeSquare  **
parameter: Clapt_before, Clapt_afterの値などをチェック(300はopen, 3300/200は1(150um)がおすすめ)
はじめは、shift_squre2view=0としgoto_LowDose=1として、Squareとview間の両倍率のFOVをあわせてどれ程度ずれたかを確認する。この値をproperties(square2view_x, square2view _y)に入れる。shift_squre2view=1とすると次回以降この値が適用されSquareとview間の両倍率の移動が楽になる。
How to do：TakeSquare後にマップからターゲットを選びadd pointsおよびgoto xyでステージ移動する。その後viewの倍率に行き(Low Dose Controlをチェック)、ターゲットをその倍率で真ん中に来るようにステージ移動し撮影する。ターゲットをクリックしたあとにshift to makerで両倍率のFOVをあわせてどれ程度ずれたかを確認する。pop upで出てきたこの値をproperties(square2view_x, square2view_y)に入れるとよい。


** 3. SPADataCollection_Screening **
動作確認のみ。
スクリプトAutoFocusRoutine を確実に動かすためにFocusメニュー”target defocus” で-1.4~-2.0にsetすることを忘れずにすること。
スクリプトAlignToHoleを動かす際にYoneoHole (Yonekura et al. 2021)を使用する場合にはuse_YL = 1にすること。このとき、YoneoHoleを起動することを忘れずに行うこととaligntohole内のuse_YLの方が優先されるので注意が必要。

** 4.FindVectorsRoutine **
動作確認のみ。穴がはっきりと視認できるポジションに移動すること。
parameters: using_ZbyV =1(ケラーの場合は0がおすすめ)
doAll=0 (1なら高さ合わせ、holealignment, FindInitVectorすべて行う)

** 5.AligncomaAndStig **
動作確認のみ。
settle_time = 10 sec
exp_time = 1(氷の厚さなどによる)

** 6. ZLPalignByFL **
動作確認。コマンドプロンプトが現れることと、JEOL PCのTemCenterのFLFが変更していあるかどうかを確認。または画像が一度でも暗くなると良い。
parameters: search_range = 40 がオススメ

** 7. SPADataCollection **
parameter: LAYERなどスクリプトparametersで決定する
byOLとbyZで確認
＊頻繁に変えるものは最初の行に記載してある。

LogImage setting, save_Rは0にすること。これを入れないと取得スピードが遅くなる。

![image](https://user-images.githubusercontent.com/34903373/209277317-75ac8e88-6124-4e7c-bff9-7785986146c5.png)