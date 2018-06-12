---
title: '[Windows 10]ログオンスクリプトのコマンドウィンドウの前面表示'
category: programming
tags:
  - windows
  - logon-script
  - cmd
date: 2018-06-12 21:38:37
---

Windows 10のログオンスクリプトでバッチファイル(.bat)を実行する際、
実行中のコマンドウィンドウを前面に表示する小ネタ。
業務で必要に迫られて実装したので、その内容をメモ。

# グループポリシーで設定できないの？
グループポリシー管理エディターの「コンピューターの構成」＞「ポリシー」＞「管理用テンプレート」＞「システム」＞「グループポリシー」＞「実行中のログオン スクリプトの命令を表示する」を「有効」にすればできそうな気がしていました。
しかし、実際に設定してみると、コマンドウィンドウは表示されるものの、ウィンドウが最小化された状態になってしまいます。

# ならどうする？
`START`コマンドを使い、ログオンスクリプト内で`cmd.exe`を再度起動し直します。
すると、新規に起動したコマンドウィンドウは前面に表示されるため、
目的である「コマンドウィンドウを前面に表示する」が実現できます。

# 実装例1 - .batファイルを2つ使用する場合
## logon_script_start.bat
`START`コマンドで新規コマンドウィンドウを開き、メイン処理が記述された.batファイル`logon_script_main.bat`を実行します。

```cmd
@ECHO OFF
START cmd.exe /C C:\logon_script_main.bat
EXIT
```

## logon_script_main.bat
メインの処理を記述します。

```cmd
@ECHO OFF
ECHO やべーやつをダウンロードしています...
ECHO やべーやつのダウンロードが完了しました！
PAUSE
EXIT
```

# 実装例2 - .batファイルを1つにまとめる場合
いやいや、.batファイルが2つもあったら管理が面倒でしょ、という方向け。
コマンドライン引数にて起動処理(コマンドウィンドウを開くだけ) or メイン処理(ビジネスロジック)を判断します。
下記の例では、第1引数が空の場合は起動処理、空出ない場合はメイン処理と判断します。

```cmd
@ECHO OFF
SET IS_MAIN_PROCESS=%1

REM 第1引数の存在判定(起動処理か？)
if "%IS_MAIN_PROCESS%"=="" (
  REM これは起動処理
  START cmd.exe /C %0 1 
  EXIT
)

REM ここからメイン処理
ECHO やべーやつをダウンロードしています...
ECHO やべーやつのダウンロードが完了しました！
PAUSE
EXIT
```

# おわりに
少々ロジックが分かりにくいので、可能であれば2ファイルに分けたほうが良いかなと思います。
既存のログオンスクリプトがあり、グループポリシーの設定を変えずに対応したい、という要件であれば
実装例2のような対応もありかなーとは思いますが、あまりやりたくはないですね……