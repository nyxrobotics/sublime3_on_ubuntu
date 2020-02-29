# sublime3_on_ubuntu 
SublimeText3の設定手順を記録します  
環境:Ubuntu16.04, kernel:4.15.0-64-generic  
  
1. SublimeText3をインストール[1]

        wget -qO - https://download.sublimetext.com/sublimehq-pub.gpg | sudo apt-key add -
        echo "deb https://download.sublimetext.com/ apt/stable/" | sudo tee /etc/apt/sources.list.d/sublime-text.list
        sudo apt update
        sudo apt-get install sublime-text

1. ポップアップを無効化[2]  

    1. 以下のスクリプトをクリップボードにコピー  
    ※markdown[コード挿入方法](https://meta.stackexchange.com/posts/173221/edit)

            #!/usr/bin/python
            # -*- coding: utf-8 -*-

            import sublime_plugin
            import subprocess
            from time import sleep
            import sys
             
            cl = lambda line: subprocess.Popen(line, shell=True, stdout=subprocess.PIPE).communicate()[0].strip()
            log = lambda message: sys.stderr.write("Log: %s\n" % message)
             
            sublimeMainWindowTitle = " - Sublime Text (UNREGISTERED)"
             
            class LicenseWindowKiller(sublime_plugin.EventListener):
             
                def seek_n_close(self):
                    sublimePid = int(cl("""wmctrl -lp | grep "%s" | awk '{print $3}'""" % sublimeMainWindowTitle).decode())
                    if sublimePid:
                        sublimeMainWindowId = cl("""wmctrl -lp | grep "%s" | awk '{print $1}'""" % sublimeMainWindowTitle).decode()
                        sublimeSecondWindowId = cl("""wmctrl -lp | grep %s | awk '{ids[$1]++}{for (id in ids){if (id != "%s"){printf id}}}'""" % (sublimePid, sublimeMainWindowId)).decode()
                        if sublimeSecondWindowId:
                            sublimeSecondWindowTitle = cl("""wmctrl -lp | grep %s | awk '{print $5}'""" % sublimeSecondWindowId).decode()
                            if not sublimeSecondWindowTitle:
                                cl("wmctrl -i -c %s" % sublimeSecondWindowId)
                                return True
                    return False
             
                def on_pre_save_async(self, *args):
                    seek = True
                    counter = 10
                    while seek:
                        sleep(.5)
                        counter -= 1
                        if counter < 0:
                            seek = False
                        seek = not self.seek_n_close()

    1. SublimeText3のメニューバーから、 "Tools --> Developer --> New Plugin ...."  

    1. さっきのスクリプトをペースト  

    1. 適当なファイル名（"popup_block.py"など）で保存（保存場所は変えない）  

1. 更新を無効化[3]  
  
1. 日本語インライン入力に対応[4][5]  
※key bindingsにctrl+spaceを設定する際、ubuntuキーボードのtext entry変更のショートカットキーと重複していると無効になるので注意
※mozcでの日本語入力時、黒背景黒文字の非常に見えにくい表示になる
※英字入力・日本語入力の切り替えはubuntuのキーボード切り替えと独立しており、ubuntu側は英字入力にしている状態でないと正しく切り替わらない。結構混乱するので要解決
※変換候補のスクロールが出てこないので不便

1. 選択部分の色変更  
    SublimeMozcInputを導入した際、入力途中の文字が大概見難い（背景色「selection」、文字色不明）  
    今回は適当にいい感じのtmThemeを持ってきて、「selection」の色を変更する。
    1. いい感じのtmTheme（Flatland）を導入[6]  
    1. 上記でインストールしたパッケージを複製して名前を変更  
        - Theme - Flatland.sublime-packageをコピー、パッケージ名を変更  
        例：Theme - NyxFlatland.sublime-package  
        - アーカイブマネージャを開き、パッケージ名を変更  
        例：Flatland Dark.sublime-theme -> NyxFlatland Dark.sublime-theme  
        - テーマファイル名も変更  
        Flatland Dark.tmTheme -> NyxFlatland Dark.tmTheme  
        Flatland Monokai.tmTheme -> NyxFlatland Monokai.tmTheme  
        - テーマファイルを編集し、選択部分の背景色「selection」を明るい灰色に変更  
        文字列検索で「<key>selection</key>」を探し、その下の行の色に関する数値を変更  
        例：

                <key>selection</key>
                <string>#adadad</string>

                <key>selectionBorder</key>
                <string>#adadad</string>

                <key>findHighlight</key>
                <string>#ffe792</string>

                <key>inactiveSelection</key>
                <string>#545454</string>




1. MarkDown編集機能（OmniMarkupPreviewerなど）[16]  
  
1. ConvertToUTF8[17]  

1. Theme - Afterglow[18]  



<参考文献>  
[1]https://qiita.com/riv/items/e8de0aa218d12429206a  
[2]https://gist.github.com/egel/b7beba6f962110596660  
[3]https://forum.sublimetext.com/t/solved-disable-update-available-pop-up/1381  
[4]https://qiita.com/riv/items/e8de0aa218d12429206a  
[5]http://yasuyuky.github.io/SublimeMozcInput/  
[16]https://qiita.com/kouichi-c-nakamura/items/e2aa565ac4571d1a5ff5  
[17]http://watarisein.hatenablog.com/entry/2014/12/15/212518  
[18]https://gist.github.com/watari53/66e46d4a541f17a0169e  
