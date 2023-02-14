# SerialEM SPA scritps for CRYO ARM series



## 前置き  introduction
このスクリプトはJEOL製CRYO ARMシリーズ(200,300,300Ⅱ)の単粒子解析用データ収集用です。他の装置(F200やTFS社製)での動作は保証できません。  
このスクリプトをインストールしたい場合は以下の手順を試してみてください。もし、わからない場合は日本電子のアプリケーションエンジニアか大阪大学大学院生命機能研究科 難波研究室 牧野(h1839[at]fbs.osaka-u.ac.jp)に相談してみてください。  
This script is for data collection of single particle analysis on JEOL's CRYO ARM series (200, 300, 300Ⅱ). It is not guaranteed to work with other instruments (F200 or TFS).　　
If you want to install this script, please try the following procedure. If you are not sure, please consult with an application engineer at JEOL or Dr. Makino (h1839[at]fbs.osaka-u.ac.jp) at Namba Laboratory, Graduate School of Frontier Biosciences, Osaka University.　　

### 日本電子の装置を使用した単粒子解析のデータ収集(SPAデータ収集)の考え方  Concept of data collection for single particle analysis (SPA data collection) using JEOL's CRYO ARMs
・日本電子の装置はステージの精度がそれほど高くありません。そのため、SerialEMの従来のやり方では正確に場所をアライメントするために色々な工夫や正確なアライメントを行うための時間が必要になってしまいます。また、LowMagからMagに移行した際のヒステリシスも大きいので頻繁な行き来はお勧めできないのも理由の一つです。
そこで、正確性を少し犠牲にして速さを追求し、ヒステリシスを考慮したのが、私たちが提供するスクリプトです。数スクエアのスクリーニングが30-60分、その後データ収集に+30-60分が目標です。大まかな流れだけをここに示します。  
JEOL's TEM does not have a very high stage accuracy. Therefore, the conventional method of SerialEM requires a lot of ingenuity to align the location accurately and time to perform accurate alignment. Another reason is that frequent back and forth is not recommended between LowMag and Mag because of the large hysteresis when transitioning from LowMag to Mag. So, we have provided a script that is fast with a slight sacrifice in accuracy and hysteresis. Our goal is 30-60 minutes for screening a few squares and then +30-60 minutes for data collection. Only a rough flow is shown here.　　

ここで注意が必要なのは一度Magに移行してからはなるべくLowMagに行かないようにすることです。どうしても必要な場合は再度ビームのアライメント調整が必要です。　　It is important to note that once you move to Mag, you should try not to go to LowMag. If it is absolutely necessary, the beam alignment must be adjusted again.　　

また、それぞれの作業を説明した動画を各項目でリンクしています。また、スクリーニングとデータ収集を行っている様子のvideoを用意する予定です(未実装)。  
詳しいマニュアルはこちらの[筑波⼤クライオ電顕・単粒⼦解析⽤測定マニュアル](https://www2.kek.jp/imss/sbrc/beamline/cryoem/221225_TsukubaCRYOARM300II_TrainingText_v1.pdf)を参照して下さい（筑波大学 安達先生ありがとうございます。本スクリプトとバージョンや名前が合わない場合がありますがご了承ください）。  
ワークフローを確認できる簡単マニュアルはこちらのリンクです  
[スクリーニング](https://github.com/fmakino/SerialEM_SPAScripts_for_CRYO_ARM_series/blob/main/ScreeningProcedure_v4.1_JP.pdf)  
[データ収集](https://github.com/fmakino/SerialEM_SPAScripts_for_CRYO_ARM_series/blob/main/DataCollectionProcedure_v4.1_JP.pdf)  
We have also provided a link in each section's YouTube. In addition, videos of screening and data collection in progress are available(not yet).
For a detailed manual, please refer to the manual for cryo-electron microscopy and single particle analysis of the University of Tsukuba here (Thank you, Dr. Adachi, University of Tsukuba). (Please note that the version and name may not match this script. It's only japanease language)
Here is a link to a simple manual where you can check the workflow.  
[screening JP](https://github.com/fmakino/SerialEM_SPAScripts_for_CRYO_ARM_series/blob/main/ScreeningProcedure_v4.1_JP.pdf)  
[data collection JP](https://github.com/fmakino/SerialEM_SPAScripts_for_CRYO_ARM_series/blob/main/DataCollectionProcedure_v4.1_JP.pdf)  

大阪大学大学院生命機能研究科で使用しているスクリプト  
[CRYO ARM 300](https://github.com/fmakino/SPAscripts-using-SerialEM-for-CRYOARMseries/blob/main/SerialEMsettings-script_Fukumura-Makino_z300_simple.txt)  
[CRYO ARM 300Ⅱ](https://github.com/fmakino/SPAscripts-using-SerialEM-for-CRYOARMseries/blob/main/SerialEMsettings-script_Fukumura-Makino_3300_simple.txt)     上記リンクからではなく、codeからzipをダウンロードしてtextファイルとしてダウンロードしてください。   
Scripts used at Graduate School of Frontier Biosciences, Osaka University
[CRYO ARM 300](https://github.com/fmakino/SPAscripts-using-SerialEM-for-CRYOARMseries/blob/main/SerialEMsettings-script_Fukumura-Makino_z300_simple.txt)  
[CRYO ARM 300II](https://github.com/fmakino/SPAscripts-using-SerialEM-for-CRYOARMseries/blob/main/SerialEMsettings-script_Fukumura-Makino_3300_simple.txt)   
Please download the zip from code and not from the above link as a TEXT file.    

## 事前準備、用意しておくこと
・SerialEMのキャリブレーションとインストール(ver4以上)が完了していること  
  
・Focus/Tuneメニュー  Drift Protection にチェックが入っていること  
    
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
  
[説明動画：PyJEM関連とhole templateについて](https://youtu.be/PwmvMNkzVo4)  
  
  
SerialEM calibration and installation (ver. 4 or higher) must be completed.  
  
The Drift Protection checkbox in the Focus/Tune menu must be checked.  
  
SetCLaperture.bat/py, SetOLaperture.bat/py, shift_FL_client.bat/py, shift_FL_server.bat/py of PyJEM programs are written in Script 40: EMproperties. WORKING_DIR_PATH folder + Tool (Default WORKING_DIR_PATH is C:\ProgramData\SerialEM, that is C:\ProgramData\SerialEM\Tool)  
  
Please consult with your local JEOL engineer about obtaining the above PyJEM programs and installing PyJEM.  
  
(This depends on your environment, such as whether or not to activate miniconda, so an application engineer can help you with this.  
How to check: Check by running each program by itself on the command prompt. First, open the cmd console, open the WORKING_DIR_PATH folder, and drag and drop it into the cmd console. Refer to the following for the arguments.  
→Open the folder, then Shift+right-click and select Open command window here.  
Example) For SetCLAperture.bat: SetCLaperture.bat 0 1   
The first CL aperture is set. (Argument 1 is 0 for the first aperture, 1 for the second aperture, and argument 2 is set for the 1-4th apertures)  
Example) For SetOLAperture.bat: SetOLaperture.bat 2 1　  
The first OL aperture is set. (Argument 1 is the same as above. Argument 2 is 0 to retrieve the OL aperture, and 1-4 to set the equivalent aperture)  
→If the program works, but you get an error that the aperture is not in the list for any of the arguments, check the arguments. If it still does not work, please contact us. For shift_FL_client.bat and shift_FL_server.bat, you can check with the script ZLPalignByFL (see below).  

Prepare a default hole template (created with the size of the frequently used hole) and copy it in advance to C:¥ProgramData¥SerialEM¥Data¥HoleImage¥hole_template.mrc  


## 新しいスクリプトのインストール方法  
新しいスクリプトをダウンロード後、任意の場所にコピー(例：C:¥ProgramData¥SerialEM¥)  
SerialEM上部のメニューscript --> Load new packageから先程コピーしたスクリプトをロードする。  
After downloading the new script, copy it to the desired location (e.g., C:¥ProgramData¥SerialEM¥)  
Load the script you just copied from the menu "script --> Load new package" at the top of SerialEM.  
  
### EMProperties の値を確認および入力    
　もし以前同様のスクリプトを使用していた場合は下記の値をそちらからコピーすると簡単で早い（変数名が変更してある可能性があるので注意が必要である）。  ここでは難波研究室CRYO ARM 300Ⅱでの使用例をのせる。  If you have used a similar script before, it is easy and fast to copy the following values from that script (note that the variable names may have been changed). Here is an example of use with CRYO ARM 300II at Namba Laboratory.  
   
__JEOL TEM type__   
・CLapt_type = ０ : # 0: CryoARM200, CryoARM200CA, CryoARM300II, F200  
                 # 1: 2nd CL aperture board, CryoARM300  
・ARM300：CRYO ARM 300(Z300), set to 1.  

__Stage setting__  
・safty_z_lower = -210 #[um], safty_z_upper = 205 #[um] :zの限界値  
・eucentric_height = 0 # [um] ：0がデフォルト  
・koehler_z_offset = -140 # [um] ：0がデフォルト、ケラーモード使う場合は-120程度入れるとよい。その場合はTakeSquare時にadjust_eucentric = 1にしないとケラーの高さにいかない。  

EN  
・safty_z_lower = -210 #[um], safty_z_upper = 205 #[um] :z limit  
・eucentric_height = 0 # [um]: 0 is default  
・koehler_z_offset = -140 # [um]: 0 is default. In that case, adjust_eucentric = 1 when TakeSquare is used, otherwise the height will not go to Keller's height.  

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
EN  
・default_spot_atlas: Spot size when creating Atlas map  
・mag_atlas: Magnification  when creating Atlas map (50 for CRYO ARM 300, 30 for CRYO ARM 200)  
・brightness_atlas: 100% is often used.  
・overlap_x, overlap_y, frame_x, frame_y: These values are obtained from navigator's full montage.  
・Full size of atlas: How many shots are taken in total.  
fullFrameX = 3  
fullFrameY = 4  
・FOV offset from Atlas to Square  
atlas2square_x = -70 # [um].  
atlas2square_y = 116.5 # [um].  
: shift to marker value when moving from Atlas to Square, if you want to update it, add to those values and write them.  


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
[説明動画：インストールとチェック項目について](https://youtu.be/AWCzy14NnQ0)  
  

### スクリプトについての注意点  
＊EMPropertiesの値は普段変更を加えない(FOV offset from Atlas to Square, FOV offset From Square to Viewは１週間に一度程度の変更)。    
＊スクリプト内のparameterはグリッドや条件によって変更しやすい  
  Notes on Scripts  
EMProperties values are not usually changed (FOV offset from Atlas to Square and FOV offset From Square to View are changed about once a week).  
Parameters in the scripts are easily changed depending on the grid and conditions.  

  
### 確認すべきスクリプト  
**1. TakeAtlas**   
parameter: Clapt_before, Clapt_afterの値などをチェック(300は0 (open), 3300/200は1(150um))  
はじめは、shift_atlas2square=0, goto_Square=1として、AtlasとSquare間の両倍率のField Of View (FOV)をあわせてどの程度ずれたかを確認する。この値をproperties(atlas2square_x, atlas2square_y)に入れる。shift_atlas2square=1とすると次回以降この値が適用されAtlasとSquare間の両倍率の移動が楽になる。  
How to do：TakeAtlas後にマップからターゲットを選びadd pointsおよびgoto xyでステージ移動する。その後Squareの倍率に行き、ターゲットをその倍率で真ん中に来るようにステージ移動し撮影する。ターゲットをクリックしたあとにshift to makerで両倍率のFOVをあわせてどれ程度ずれたかを確認する。pop upで出てきたこの値をproperties(atlas2square_x, atlas2square_y)に入れるとよい。  
[説明動画](https://youtu.be/rGww73Szdy0)  
  parameter: Check Clapt_before, Clapt_after values, etc. (300 is 0 (open), 3300/200 is 1 (150um))  
At first, shift_atlas2square=0, goto_Square=1, and check how much the Field Of View (FOV) of both magnifications between Atlas and Square are shifted together. Set shift_atlas2square=1 to apply this value from the next time and make it easier to move both magnifications between Atlas and Square.  
How to do: After TakeAtlas, select a target from the map and move the stage by add points and goto xy. Then go to Square magnification and move the target to the center of the stage at that magnification and record. After clicking on the target, use shift to maker to adjust the FOV of both magnifications and check how much the FOV has shifted, and put this value from pop up into properties(atlas2square_x, atlas2square_y).  
[Youtube](https://youtu.be/rGww73Szdy0)  

  
**2. TakeSquare**  
parameter: Clapt_before, Clapt_afterの値などをチェック(300はopen, 3300/200は1(150um)がおすすめ)  
はじめは、shift_squre2view=0としgoto_LowDose=1として、Squareとview間の両倍率のFOVをあわせてどれ程度ずれたかを確認する。この値をproperties(square2view_x, square2view _y)に入れる。shift_squre2view=1とすると次回以降この値が適用されSquareとview間の両倍率の移動が楽になる。  
How to do：TakeSquare後にマップからターゲットを選びadd pointsおよびgoto xyでステージ移動する。その後viewの倍率に行き(Low Dose Controlをチェック)、ターゲットをその倍率で真ん中に来るようにステージ移動し撮影する。ターゲットをクリックしたあとにshift to makerで両倍率のFOVをあわせてどれ程度ずれたかを確認する。pop upで出てきたこの値をproperties(square2view_x, square2view_y)に入れるとよい。    
[説明動画](https://youtu.be/6RnbGBvGPgI)
parameter: Check the values of Clapt_before, Clapt_after, etc. (open for 300, 1 (150um) for 3300/200 is recommended)  
At first, set shift_squre2view=0 and goto_LowDose=1 to check how much the FOV of both magnifications between square and view are shifted together. If shift_squre2view=1, this value will be applied from the next time onward to make it easier to move both magnifications between square and view.  
How to do: After TakeSquare, select a target from the map and move the stage by add points and goto xy. Then go to view magnification (check Low Dose Control) and stage the target so that it is in the center at that magnification and record. After clicking on the target, use shift to maker to adjust the FOV of both magnifications and check how much the FOV has shifted, and put this value from pop up into properties(square2view_x, square2view_y).  
[Youtube](https://youtu.be/6RnbGBvGPgI)

**3. SPADataCollection_Screening**  
動作確認のみ。  
スクリプトAutoFocusRoutine を確実に動かすためにFocusメニュー”target defocus” で-1.4~-2.0にsetすることを忘れずにすること。  
スクリプトAlignToHoleを動かす際にYoneoLocrのYoneoHole ([Yonekura et al. 2021](https://www.nature.com/articles/s42003-021-02577-1)　[入手先](https://github.com/YonekuraLab/yoneoLocr))を使用する場合にはuse_YL = 1にすること。このとき、YoneoHoleを起動することを忘れずに行うこととaligntohole内のuse_YLの方が優先されるので注意が必要。  
[説明動画](https://youtu.be/VLQfcRfwaqk)  
  Confirmation of operation only.  
To make sure the script AutoFocusRoutine works, set the "target defocus" in the Focus menu to -1.4 to -2.0.  
If you use YoneoLocr's YoneoHole (Yonekura et al. 2021, available from Yonekura et al. 2021) to run the script AlignToHole, set use_YL = 1. At this time, do not forget to activate YoneoHole, and note that use_YL in aligntohole has priority over use_YL.  
[Youtube](https://youtu.be/VLQfcRfwaqk) 

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
[説明動画](https://youtu.be/p5eJ8c1EzUM)  

Confirmation of operation only. Move to a position where the hole is clearly visible.  
parameters: skip_focusing = 0 # 0: No, 1:Yes  
skip_holeAlign = 0 # 0: No, 1:Yes  
skip_InitVec = 1 # 0: No, 1:Yes  
skip_checkAccuracy = 0 # 0: No, 1:Yes  
using_ZbyV =1 (0 is recommended by default for Keller)  
and see how it goes. In case of Namba Lab, the following parameters are often used after screening.  
skip_focusing = 0 # 0: No, 1:Yes  
skip_holeAlign = 1 # 0: No, 1:Yes  
skip_InitVec = 1 # 0: No, 1:Yes  
skip_checkAccuracy = 1 # 0: No, 1:Yes  
using_ZbyV = 0 (0 is recommended by default for Keller)  
[Youtube](https://youtu.be/p5eJ8c1EzUM)  


**5.AligncomaAndStig**  
動作確認のみ。  
settle_time = 10 sec  
exp_time = 1(氷の厚さなどによる)  
もし、うまく動かない場合：高さ調整かAutoFocusがうまく行ってない場合が多い。どちらも、AutoFocusのキャリブレーションで直る場合が多い。  
[説明動画](https://youtu.be/be8YbSjC8J4)  
  Confirmation of operation only.  
settle_time = 10 sec  
exp_time = 1 (depending on ice thickness, etc.)  
If it does not work: In most cases, either the height adjustment or AutoFocus is not working properly. In many cases, both can be fixed by calibrating AutoFocus.  
[Youtube](https://youtu.be/be8YbSjC8J4)  


**6. ZLPalignByFL**  
動作確認。コマンドプロンプトが現れることと、JEOL PCのTemCenterのFLFが変更していあるかどうかを確認。または画像が一度でも暗くなると良い。  
parameters: search_range = 40 がオススメ  
もし、うまく動かない場合は'WORKING_DIR_PATH¥Tool'にshift_FL_client.bat/py, shift_FL_server.bat/pyがコピーされていること、また、最新のものであるか確認してください。
[説明動画](https://youtu.be/9jEgw5Tsib0)
Confirmation of operation.  
Check that the command prompt appears and that the FLF in TemCenter on the JEOL PC has been changed. Or if the image darkens at least once.  
parameters: search_range = 40 is recommended  
If it does not work, make sure that shift_FL_client.bat/py and shift_FL_server.bat/py are copied to 'WORKING_DIR_PATH¥Tool' and that they are up to date.   
[Youtube](https://youtu.be/9jEgw5Tsib0)


**7. SPADataCollection**  
parameter: LAYERなどスクリプトparametersで決定する  
byOLとbyZで確認  
＊頻繁に変えるものは最初の行に記載してある。  

    
LogImage setting, save_Rは0にすること。これを入れないと取得スピードが遅くなる。  
parameter: determined by script parameters such as LAYER  
Check byOL and byZ  
*Frequently changed items are listed in the first line.  

LogImage setting, save_R should be 0. If this is not included, the speed of acquisition will be slow.

*Autofocus時のFocus設定に関するバグをfixしました(high:-1.0, Low:-2.0。 step 0.2で-2.2の設定まで行ってしまう） 2023/1/30
*ケラーモードを選んでTakeSquareした場合にadjust eucentricが反映されないバグをfixしました。 2023/2/14
