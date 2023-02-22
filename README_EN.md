
## Introduction

This script is for data collection of single particle analysis on JEOL's CRYO ARM series (200, 300, 300Ⅱ). It is not guaranteed to work with other instruments (F200 or TFS).　　
If you want to install this script, please try the following procedure. If you are not sure, please consult with an application engineer at JEOL or Dr. Makino (h1839[at]fbs.osaka-u.ac.jp) at Namba Laboratory, Graduate School of Frontier Biosciences, Osaka University.　　

###  Concept of data collection for single particle analysis (SPA data collection) using JEOL's CRYO ARMs  
JEOL's TEM does not have a very high stage accuracy. Therefore, the conventional method of SerialEM requires a lot of ingenuity to align the location accurately and time to perform accurate alignment. Another reason is that frequent back and forth is not recommended between LowMag and Mag because of the large hysteresis when transitioning from LowMag to Mag. So, we have provided a script that is fast with a slight sacrifice in accuracy and hysteresis. Our goal is 30-60 minutes for screening a few squares and then +30-60 minutes for data collection. Only a rough flow is shown here.　　

It is important to note that once you move to Mag, you should try not to go to LowMag. If it is absolutely necessary, the beam alignment must be adjusted again.　　


We have also provided a link in each section's YouTube. In addition, videos of screening and data collection in progress are available(not yet).
For a detailed manual, please refer to the manual[Tsukuba University Manual](https://www2.kek.jp/imss/sbrc/beamline/cryoem/221225_TsukubaCRYOARM300II_TrainingText_v1.pdf) for cryo-electron microscopy and single particle analysis of the University of Tsukuba here (Thank you, Dr. Adachi, University of Tsukuba). (Please note that the version and name may not match this script. It's only japanease language)  
Here is a link to a simple manual where you can check the workflow.  
[screening JP](https://github.com/fmakino/SerialEM_SPAScripts_for_CRYO_ARM_series/blob/main/ScreeningProcedure_v4.1_JP.pdf)  
[data collection JP](https://github.com/fmakino/SerialEM_SPAScripts_for_CRYO_ARM_series/blob/main/DataCollectionProcedure_v4.1_JP.pdf)  

Scripts used at Graduate School of Frontier Biosciences, Osaka University  
[CRYO ARM 300](https://github.com/fmakino/SPAscripts-using-SerialEM-for-CRYOARMseries/blob/main/SerialEMsettings-script_Fukumura-Makino_z300_simple.txt)  
[CRYO ARM 300II](https://github.com/fmakino/SPAscripts-using-SerialEM-for-CRYOARMseries/blob/main/SerialEMsettings-script_Fukumura-Makino_3300_simple.txt)   
Please download the zip from code and not from the above link as a TEXT file.    

## Preparation  
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

[Youtube](https://youtu.be/zP3PCU6qTfY)  

## How to install new scripts  

After downloading the new script, copy it to the desired location (e.g., C:¥ProgramData¥SerialEM¥)  
Load the script you just copied from the menu "script --> Load new package" at the top of SerialEM.  
[Youtube](https://youtu.be/m1iIJxrMrSQ)  

### Check and input values in EMProperties    
　If you have used a similar script before, it is easy and fast to copy the following values from that script (note that the variable names may have been changed). Here is an example of use with CRYO ARM 300II at Namba Laboratory.  
   
__JEOL TEM type__   
・CLapt_type = ０ : # 0: CryoARM200, CryoARM200CA, CryoARM300II, F200  
                 # 1: 2nd CL aperture board, CryoARM300  
・ARM300：CRYO ARM 300(Z300), set to 1.  

__Stage setting__  
・safty_z_lower = -210 #[um], safty_z_upper = 205 #[um] :z limit  
・eucentric_height = 0 # [um]: 0 is default  
・koehler_z_offset = -140 # [um]: 0 is default. In that case, adjust_eucentric = 1 when TakeSquare is used, otherwise the height will not go to Keller's height.  

__Setting for Atlas__  
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
・default_SquareMag = 150: Magnification when acquiring squaremap  
・spot_square = 4: Spot size when acquiring squaremap  
・brightness_square = 100: 100% is often used. It is recommended to adjust it together with FL focus when  you want to insert energy filter.
・FOV offset From Square to View  
square2view_x = 20 # [um].  
square2view_y = 30 # [um].  
: Value of shift to marker when moving from Square to View, if you want to update it, add to those values and describe it.  
  

__Python Call setting__   
・Find out where each program is located    
  
__YoneoLocr__  
・progdir = C:\Users\VALUEDGATANCUSTOMER\Desktop\yoneoLocr-main : If you use YoneoLocr for hole align, specify this variable progdir as the place where the program refers to.  
You need to install YoneoLocr in advance. Please refer to YoneoLocr's [website](https://github.com/YonekuraLab/yoneoLocr) for more information about this. Also, please remember to cite it in your papers if you use it.

There are many other values, but these are the minimum necessary.  

### Notes on Scripts  
EMProperties values are not usually changed (FOV offset from Atlas to Square and FOV offset From Square to View are changed about once a week).  
Parameters in the scripts are easily changed depending on the grid and conditions.  

  
### Checking Scripts
**1. TakeAtlas**   
  parameter: Check Clapt_before, Clapt_after values, etc. (300 is 0 (open), 3300/200 is 1 (150um))  
At first, shift_atlas2square=0, goto_Square=1, and check how much the Field Of View (FOV) of both magnifications between Atlas and Square are shifted together. Set shift_atlas2square=1 to apply this value from the next time and make it easier to move both magnifications between Atlas and Square.  
How to do: After TakeAtlas, select a target from the map and move the stage by add points and goto xy. Then go to Square magnification and move the target to the center of the stage at that magnification and record. After clicking on the target, use shift to maker to adjust the FOV of both magnifications and check how much the FOV has shifted, and put this value from pop up into properties(atlas2square_x, atlas2square_y).  
[Youtube](https://youtu.be/DOBqU_4LCY8)  

  
**2. TakeSquare**  
parameter: Check the values of Clapt_before, Clapt_after, etc. (open for 300, 1 (150um) for 3300/200 is recommended)  
At first, set shift_squre2view=0 and goto_LowDose=1 to check how much the FOV of both magnifications between square and view are shifted together. If shift_squre2view=1, this value will be applied from the next time onward to make it easier to move both magnifications between square and view.  
How to do: After TakeSquare, select a target from the map and move the stage by add points and goto xy. Then go to view magnification (check Low Dose Control) and stage the target so that it is in the center at that magnification and record. After clicking on the target, use shift to maker to adjust the FOV of both magnifications and check how much the FOV has shifted, and put this value from pop up into properties(square2view_x, square2view_y).  
[Youtube]([https://youtu.be/WwYUhpgNzqQ)  

**3. SPADataCollection_Screening**  
  Confirmation of operation only.  
To make sure the script AutoFocusRoutine works, set the "target defocus" in the Focus menu to -1.4 to -2.0.  
If you use YoneoLocr's YoneoHole (Yonekura et al. 2021, available from Yonekura et al. 2021) to run the script AlignToHole, set use_YL = 1. At this time, do not forget to activate YoneoHole, and note that use_YL in aligntohole has priority over use_YL.  
[Youtube](https://youtu.be/SymgfCqZiIg)  

**4.FindVectorsRoutine**  

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
[Youtube](https://youtu.be/DkAXpnlukEA)  


**5.AligncomaAndStig**  
  Confirmation of operation only.  
settle_time = 10 sec  
exp_time = 1 (depending on ice thickness, etc.)  
If it does not work: In most cases, either the height adjustment or AutoFocus is not working properly. In many cases, both can be fixed by calibrating AutoFocus.  
[Youtube](https://youtu.be/ngeKlpl55SU)  


**6. ZLPalignByFL**  
Confirmation of operation.  
Check that the command prompt appears and that the FLF in TemCenter on the JEOL PC has been changed. Or if the image darkens at least once.  
parameters: search_range = 40 is recommended  
If it does not work, make sure that shift_FL_client.bat/py and shift_FL_server.bat/py are copied to 'WORKING_DIR_PATH¥Tool' and that they are up to date.   
[Youtube](https://youtu.be/wCDgW2M3zrE)  


**7. SPADataCollection**  
parameter: determined by script parameters such as LAYER  
Check byOL and byZ  
*Frequently changed items are listed in the first line.  

LogImage setting, save_R should be 0. If this is not included, the speed of acquisition will be slow.

*Autofocus時のFocus設定に関するバグをfixしました(high:-1.0, Low:-2.0。 step 0.2で-2.2の設定まで行ってしまう） 2023/1/30
*ケラーモードを選んでTakeSquareした場合にadjust eucentricが反映されないバグをfixしました。 2023/2/14
