![image](https://github.com/user-attachments/assets/8b503322-9429-48b2-9158-4c02f7806925)# SerialEM SPA scritps for CRYO ARM series



## 前置き  introduction
このスクリプトはJEOL製CRYO ARMシリーズ(200,300,300Ⅱ)の単粒子解析用データ収集用です。他の装置(F200やTFS社製)での動作は保証できません。  
このスクリプトをインストールしたい場合は以下の手順を試してみてください。もし、わからない場合は日本電子のアプリケーションエンジニアか大阪大学大学院生命機能研究科 難波研究室 牧野(h1839[at]fbs.osaka-u.ac.jp)に相談してみてください。  
*更新は下に記載しています。

This script is for data collection of single particle analysis on JEOL's CRYO ARM series (200, 300, 300Ⅱ). It is not guaranteed to work with other instruments (F200 or TFS).　　
If you want to install this script, please try the following procedure. If you are not sure, please consult with an application engineer at JEOL or Dr. Makino (h1839[at]fbs.osaka-u.ac.jp) at Namba Laboratory, Graduate School of Frontier Biosciences, Osaka University.　　

English version is [here](https://github.com/fmakino/SerialEM_SPAScripts_for_CRYO_ARM_series/blob/main/README_EN.md).

### 日本電子の装置を使用した単粒子解析のデータ収集(SPAデータ収集)の考え方  
・日本電子の装置はステージの精度がそれほど高くありません。そのため、SerialEMの従来のやり方では正確に場所をアライメントするために色々な工夫や正確なアライメントを行うための時間が必要になってしまいます。また、LowMagからMagに移行した際のヒステリシスも大きいので頻繁な行き来はお勧めできないのも理由の一つです。
そこで、正確性を少し犠牲にして速さを追求し、ヒステリシスを考慮したのが、私たちが提供するスクリプトです。これはOsaka Frameworkと呼ばれるものです。数スクエアのスクリーニングが30-60分、その後データ収集に+30-60分が目標です。大まかな流れだけをここに示します。  

ここで注意が必要なのは一度Magに移行してからはなるべくLowMagに行かないようにすることです。どうしても必要な場合は再度ビームのアライメント調整が必要です。　

また、それぞれの作業を説明した動画を各項目でリンクしています。また、スクリーニングとデータ収集を行っている様子のvideoを用意する予定です(未実装)。  
詳しいマニュアルはこちらの[筑波⼤クライオ電顕・単粒⼦解析⽤測定マニュアル](https://www2.kek.jp/imss/sbrc/beamline/cryoem/221225_TsukubaCRYOARM300II_TrainingText_v1.pdf)を参照して下さい（筑波大学 安達先生ありがとうございます。本スクリプトとバージョンや名前が合わない場合がありますがご了承ください）。  
ワークフローを確認できる簡単マニュアルはこちらのリンクです  
[スクリーニング](https://github.com/fmakino/SerialEM_SPAScripts_for_CRYO_ARM_series/blob/main/ScreeningProcedure_v4.1_JP.pdf)  
[データ収集](https://github.com/fmakino/SerialEM_SPAScripts_for_CRYO_ARM_series/blob/main/DataCollectionProcedure_v4.1_JP.pdf)  
  
大阪大学大学院生命機能研究科で使用しているスクリプト  
[CRYO ARM 300](https://github.com/fmakino/SPAscripts-using-SerialEM-for-CRYOARMseries/blob/main/SerialEMsettings-script_Fukumura-Makino_z300_simple.txt)  
[CRYO ARM 300Ⅱ](https://github.com/fmakino/SPAscripts-using-SerialEM-for-CRYOARMseries/blob/main/SerialEMsettings-script_Fukumura-Makino_3300_simple.txt)  

PyJEMを使わないバージョン  
[CRYO ARM 300](https://github.com/fmakino/SerialEM_SPAScripts_for_CRYO_ARM_series/blob/main/SerialEMsettings-script_Fukumura-Makino_z300_simple_withoutPyJEM.txt)    
[CRYO ARM 300Ⅱ](https://github.com/fmakino/SerialEM_SPAScripts_for_CRYO_ARM_series/blob/main/SerialEMsettings-script_Fukumura-Makino_3300_simple_withoutPyJEM.txt)  

このとき、CL apertureが2段の場合はProperty fileに "JeolHasExtraApertures 1"を追加すること。EMPropertiesのCLapt_typeの値は、0は上段のCL aperture, 1は下段のCL aperture (SetApertureSizeのコマンドと同様の動き)となる。
また、ZLPAlignByFLは "RefineZLP 0 1"に変更した。  
```bash　　
ScriptName EMProperties


# CL aperture
# 1: CryoARM200, CryoARM200CA, CryoARM300II, F200
# 0 or 1: Two CL aperture of first gen CryoARM300, 0 is 1st, 1 is 2nd CL aperture.
CLapt_type = 1

```
BatchでGlobal Map (Atlas map)を実行できるスクリプトを別途追加しました (4.1の最新以上)。この際プロパティファイルにJeolHasNitrogenClassを2に設定しておく必要があります。TemExternalのバージョンについては現在調査中です。  

[Global Map](https://github.com/fmakino/SerialEM_SPAScripts_for_CRYO_ARM_series/blob/main/Global_batch_withouPyJEM.txt)  

TakeAtlasを呼び出すため、上記のスクリプトに追加する形で実装してください。   
【使い方】  POSITIONS_TO_MONTAGEにBatchでGlobal Mapを取得したいCartridgeのIDを記述する。もし、存在しないID番号があればスキップします。　　
```bash　　
# ---------------------------------------------------------------------
# Position 0 - on the column
# Positions 1-12 -> storage
# Montage_All set to 1 to auto-determine sample IDs and montage all of them
# or set to 0 and fill in the Positions_to_montage matrix with at least 2 numbers.
POSITIONS_TO_MONTAGE = { 888 889 } #input IDs keep space after number. e.g. {### }
# ---------------------------------------------------------------------

```




上記リンクからではなく、codeからzipをダウンロードしてtextファイルとしてダウンロードしてください。 
   

## 事前準備、用意しておくこと
・SerialEMのキャリブレーションとインストール(ver4.1以上 推奨)が完了していること  
  
・Focus/Tuneメニュー  Drift Protection にチェックが入っていること  

＊PyJEMを使わないスクリプトを使用する場合は以下の３つは不要です。
・PyJEMプログラム群のSetCLaperture.bat/py, SetOLaperture.bat/py, shift_FL_client.bat/py, shift_FL_server.bat/pyをスクリプト40: EMproperties内に書いてある  WORKING_DIR_PATH のフォルダ+Tool(DefaultのWORKING_DIR_PATHはC:\ProgramData\SerialEM, つまりC:\ProgramData\SerialEM\Tool)に入れること  
  
・上記のPyJEMプログラム群の入手とPyJEMのインストールについては近くの日本電子のエンジニア等にご相談下さい。
  
・上で入れたPyJEMプログラムは単体(batファイル)で動くか確認すること(minicondaをactivateするかどうかなど、環境次第なのでこれはアプリケーション技術員が対応)  
**確認方法**：それぞれのプログラムをコマンドプロンプト上で単体で動かしてチェックする。まず、cmdコンソールを開き、WORKING_DIR_PATHのフォルダを開いて、cmdコンソールにドラッグ・アンド・ドロップする。引数は以下を参考にする。  
→もしくはフォルダを開いた後Shift+右クリックでOpen command window hereを選択する。  
例)SetCLAperture.batの場合： SetCLaperture.bat 0 1   
 1番のCL apertureがセットされる。(引数1は0で1段目のアパチャ、1で2段目のアパチャ、引数2は1-4番目のアパチャのset)  
例)SetOLAperture.batの場合： SetOLaperture.bat 2 1　  
 1番のOL apertureがセットされる。(引数1は上と同じ。引数2は0でアパチャの取り出し、1-4でそれに相当するアパチャのset)  
→プログラムは動くが、いずれの引数でもlistにないというエラーが出る場合は引数を確認。  それでも駄目な場合は連絡下さい。
shift_FL_client.bat, shift_FL_server.batについてはスクリプトZLPalignByFLで確認できる（後述）  
  
・デフォルトのホールテンプレート(よく使うホールの大きさで作成しておく)を準備し、C:¥ProgramData¥SerialEM¥Data¥HoleImage¥hole_template.mrc  
に事前にコピーしておくこと。  
  
[説明動画：PyJEM関連とhole templateについて](https://youtu.be/zP3PCU6qTfY)


## 新しいスクリプトのインストール方法  
新しいスクリプトをダウンロード後、任意の場所にコピー(例：C:¥ProgramData¥SerialEM¥)  
SerialEM上部のメニューscript --> Load new packageから先程コピーしたスクリプトをロードする。  
[説明動画：インストールについて](https://youtu.be/m1iIJxrMrSQ)  
  
### EMProperties の値を確認および入力    
　もし以前同様のスクリプトを使用していた場合は下記の値をそちらからコピーすると簡単で早い（変数名が変更してある可能性があるので注意が必要である）。  ここでは難波研究室CRYO ARM 300Ⅱでの使用例をのせる。    
   
__JEOL TEM type__   
・CLapt_type = ０ : # 0: CryoARM200, CryoARM200CA, CryoARM300II, F200  
                 # 1: 2nd CL aperture board, CryoARM300  
・ARM300：CRYO ARM 300(Z300), set to 1.  

__Stage setting__  
・safty_z_lower = -210 #[um], safty_z_upper = 205 #[um] :zの限界値  
・eucentric_height = 0 # [um] ：0がデフォルト  
・koehler_z_offset = -140 # [um] ：0がデフォルト、ケラーモード使う場合は-120程度入れるとよい。その場合はTakeSquare時にadjust_eucentric = 1にしないとケラーの高さにいかない。  

__Setting for Atlas__  
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

__Setting for Square__  
・default_SquareMag = 150：squaremapを取得するときの倍率  
・spot_square = 4：squaremapを取得するときのspot size  
・brightness_square = 100：100%が多く使われる。ただしenergy filterを入れる場合はその限りではなく、ちょうどよい明るさが存在する。FL focusと合わせて調整するとよい。  
・FOV offset From Square to View  
        square2view_x = 20 # [um]    
        square2view_y = 30 # [um]    
: SquareからViewに移行したときのshift to markerの値、もしアップデートしたい場合はそれらの値に足し算して記述すること  

__Python Call setting__   
各プログラムの場所を確認  
  
__YoneoLocr__  
・progdir = C:\Users\VALUEDGATANCUSTOMER\Desktop\yoneoLocr-main ： Hole alignにYoneoLocrを使用する場合はこの変数progdirにプログラムが参照する場所として指定する。  
＊事前にYoneoLocrのインストールなどが必要です。これについてはYoneoLocrの[サイト](https://github.com/YonekuraLab/yoneoLocr)を参照してください。また、使用した場合は論文への引用も忘れないようにしてください。

他にも沢山の値が存在するが必要最低限なものは以上。  

  

### スクリプトについての注意点  
＊EMPropertiesの値は普段変更を加えない(FOV offset from Atlas to Square, FOV offset From Square to Viewは１週間に一度程度の変更)。    
＊スクリプト内のparameterはグリッドや条件によって変更しやすい  


  
### 確認すべきスクリプト  
**1. TakeAtlas**   
parameter: Clapt_before, Clapt_afterの値などをチェック(300は0 (open), 3300/200は1(150um))  
はじめは、shift_atlas2square=0, goto_Square=1として、AtlasとSquare間の両倍率のField Of View (FOV)をあわせてどの程度ずれたかを確認する。この値をproperties(atlas2square_x, atlas2square_y)に入れる。shift_atlas2square=1とすると次回以降この値が適用されAtlasとSquare間の両倍率の移動が楽になる。  
How to do：TakeAtlas後にマップからターゲットを選びadd pointsおよびgoto xyでステージ移動する。その後Squareの倍率に行き、ターゲットをその倍率で真ん中に来るようにステージ移動し撮影する。ターゲットをクリックしたあとにshift to makerで両倍率のFOVをあわせてどれ程度ずれたかを確認する。pop upで出てきたこの値をproperties(atlas2square_x, atlas2square_y)に入れるとよい。  
[説明動画](https://youtu.be/DOBqU_4LCY8)

  
**2. TakeSquare**  
parameter: Clapt_before, Clapt_afterの値などをチェック(300はopen, 3300/200は1(150um)がおすすめ)  
はじめは、shift_squre2view=0としgoto_LowDose=1として、Squareとview間の両倍率のFOVをあわせてどれ程度ずれたかを確認する。この値をproperties(square2view_x, square2view _y)に入れる。shift_squre2view=1とすると次回以降この値が適用されSquareとview間の両倍率の移動が楽になる。  
How to do：TakeSquare後にマップからターゲットを選びadd pointsおよびgoto xyでステージ移動する。その後viewの倍率に行き(Low Dose Controlをチェック)、ターゲットをその倍率で真ん中に来るようにステージ移動し撮影する。ターゲットをクリックしたあとにshift to makerで両倍率のFOVをあわせてどれ程度ずれたかを確認する。pop upで出てきたこの値をproperties(square2view_x, square2view_y)に入れるとよい。    
[説明動画](https://youtu.be/WwYUhpgNzqQ)

**3. SPADataCollection_Screening**  
動作確認のみ。  
スクリプトAutoFocusRoutine を確実に動かすためにFocusメニュー”target defocus” で-1.4~-2.0にsetすることを忘れずにすること。  
スクリプトAlignToHoleを動かす際にYoneoLocrのYoneoHole ([Yonekura et al. 2021](https://www.nature.com/articles/s42003-021-02577-1)　[入手先](https://github.com/YonekuraLab/yoneoLocr))を使用する場合にはuse_YL = 1にすること。このとき、YoneoHoleを起動することを忘れずに行うこととaligntohole内のuse_YLの方が優先されるので注意が必要。  
[説明動画](https://youtu.be/SymgfCqZiIg)

**4.FindVectorsRoutine**  
動作確認のみ。穴がはっきりと視認できるポジションに移動すること。  
parameters: 
skip_focusing = 0 # 0: No, 1:Yes  
skip_holeAlign = 0 # 0: No, 1:Yes  
skip_InitVec = 1 # 0: No, 1:Yes  
skip_checkAccuracy = 0 # 0: No, 1:Yes  
using_ZbyV =1 (ケラーの場合はデフォルトで0がおすすめ)  
で走らせて様子を見る。難波研の場合はスクリーニング後が多いので以下のパラメータが多い。  
skip_focusing = 0 # 0: No, 1:Yes  
skip_holeAlign = 1 # 0: No, 1:Yes  
skip_InitVec = 1 # 0: No, 1:Yes  
skip_checkAccuracy = 1 # 0: No, 1:Yes  
using_ZbyV = 0 (ケラーの場合はデフォルトで0がおすすめ)    
[説明動画]( https://youtu.be/DkAXpnlukEA)

**5.AligncomaAndStig**  
動作確認のみ。  
settle_time = 10 sec  
exp_time = 1(氷の厚さなどによる)  
もし、うまく動かない場合：高さ調整かAutoFocusがうまく行ってない場合が多い。どちらも、AutoFocusのキャリブレーションで直る場合が多い。  
[説明動画](https://youtu.be/ngeKlpl55SU)

**6. ZLPalignByFL**  
動作確認。コマンドプロンプトが現れることと、JEOL PCのTemCenterのFLFが変更していあるかどうかを確認。または画像が一度でも暗くなると良い。  
parameters: search_range = 40 がオススメ  
もし、うまく動かない場合は'WORKING_DIR_PATH¥Tool'にshift_FL_client.bat/py, shift_FL_server.bat/pyがコピーされていること、また、最新のものであるか確認してください。
[説明動画](https://youtu.be/wCDgW2M3zrE)


**7. SPADataCollection**  
parameter: LAYERなどスクリプトparametersで決定する  
byOLとbyZで確認  
＊頻繁に変えるものは最初の行に記載してある。  

＊TakeAtlasではSerialEM4.0以上で発生するバグ(Montage作成時に2つ目以上の画像取得でエラーになる。回避策としてダミーを作成して対応)に対応しております。 <-- 2024/07/24 SerialEM 4.1 stableからバグ修正を確認してます
*Autofocus時のFocus設定に関するバグをfixしました(high:-1.0, Low:-2.0。 step 0.2で-2.2の設定まで行ってしまう） 2023/1/30  
*ケラーモードを選んでTakeSquareした場合にadjust eucentricが反映されないバグをfixしました。 2023/2/14  
＊shift_FL_server.bat の中でshift_FL_server.pyを呼び出す際にstartをつけてbackground処理するようにしてください(下記は例です)。そうしないと２回目以降のFL focusの自動調整時に止まることがあります。 2023/6/2　　
＊PyJEMを使わないバージョン(without PyJEM)とBatchGlobalMapをアップロードしました。 2024/7/25

```bash　　
call C:\ProgramData\Miniconda3\Scripts\activate.bat
call activate vjem38

start python C:\ProgramData\SerialEM\PyTool\shift_FL_server.py
```
＊ CRYO ARM 200用のスクリプト　"SerialEMsettings-script_Fukumura-Makino_z200_simple.txt"をアップロードしました。yoneohole起動時の動作のみ異なります。 2023/6/2　　

*スクリプト ReadVectors_Recordmode の追加。
これはRefineVectorsのhole vecotrの値をserialEM buildinのmulti hole 撮影を動かすために使用する。
具体的にはRefineVectors後のhole vecotrをSetRegularHoleVectorsに読み込ませる。
この際、parametersのuse_multiR=2に変更することを忘れないこと 2024/4/4　　
