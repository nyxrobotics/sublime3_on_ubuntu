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
  
1. MarkDown編集機能（OmniMarkupPreviewerなど）[6]  
  
1. ConvertToUTF8[7]

<参考文献>  
[1]https://qiita.com/riv/items/e8de0aa218d12429206a  
[2]https://gist.github.com/egel/b7beba6f962110596660  
[3]https://forum.sublimetext.com/t/solved-disable-update-available-pop-up/1381  
[4]https://qiita.com/riv/items/e8de0aa218d12429206a  
[5]http://yasuyuky.github.io/SublimeMozcInput/  
[6]https://qiita.com/kouichi-c-nakamura/items/e2aa565ac4571d1a5ff5  
[7]http://watarisein.hatenablog.com/entry/2014/12/15/212518
