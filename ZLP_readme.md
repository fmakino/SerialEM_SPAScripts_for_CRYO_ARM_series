## RefineZLP 0 1 がうまく動作しない場合

＊ 日本電子の電顕をPyJEM使わずSerialEM経由でコントロール出来るようにするプラグインをダウンロードおよびインストールする必要がある。
コチラ [https://bio3d.colorado.edu/ftp/SerialEM/Tools/JeolTemExtPlugin/]


ScriptName ZLPAlignByFL
の

RunInShell $shiftFLのところをすべてコメントアウト(消去)して、下記のように変更する。
```
#Runinshell $shiftFL $FL_min -->
plugin BM_JEOL GetFLf
curFlf = $RepVal1 + $Fl_min
plugin BM_JEOL SetFLf $curFlf

#Ruinshell $shiftFL $search_step -->
plugin BM_JEOL GetFLf
curFlf = $RepVal1 + $search_step
plugin BM_JEOL SetFLf $curFlf

#Ruinshell $shiftFL $FL_shift_fromEnd -->
plugin BM_JEOL GetFLf
curFlf = $RepVal1 + $FL_shift_fromEnd
plugin BM_JEOL SetFLf $curFlf
```

