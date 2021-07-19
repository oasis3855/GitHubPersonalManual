# Git環境設定

## Windowsでのgitconfig
``` .gitconfig ``` はユーザディレクトリ直下（``` C:\Users\ユーザ名\ ```）に作成する

### diff表示で、GUIツール WinMerge を使う方法
``` .gitconfig ``` に対して次の設定を記述する
```
[diff]
    tool = winmerge
    guitool = winmerge
[difftool "winmerge"]
    path = 'C:/Program Files/OnlineSoftware/TextEditor/WinMerge/WinMergeU.exe'
    cmd = "'C:/Program Files/OnlineSoftware/TextEditor/WinMerge/WinMergeU.exe' -e -u -r $LOCAL $REMOTE"
[alias]
    windiff = difftool -y -d -t winmerge
```

WinMergeのコマンドラインオプション
|-e|ESCキーでWinMergeを閉じる|
|-f *.cpp|*.cppファイルのみ対象とする|
|-r|サブディレクトリをすべて対象とする|
|-s|複数のウィンドウを開かず、シングル・インスタンスで実行する|
|-u|最近使用した項目に追加しない|
|-wl, -wr|左, 右の表示部を読み取り専用として開く|

git-diffのコマンドラインオプション
|-d|--dir-diff|ディレクトリ差分を実行|
|-y|--no-prompt|diffツールを表示する前にプロンプトを出さない|
|-t ツール|--tool=ツール|指定したdiffツールを利用する|

コマンドラインより、``` git windiff コミットNo1 コミットNo2 ```（aliasを用いる場合）を実行する

