# Git環境設定

## 基本的な環境設定
### .gitconfig
グローバル設定のための `.gitconfig` ファイルはユーザディレクトリ直下に作成する
- Linuxの場合 : `/home/ユーザ/.gitconfig`
- Windowsの場合 : `C:\Users\ユーザ\.gitconfig`

### 状態表示
グローバル設定の表示
```
git config --global -l
```
現在のリポジトリの表示（設定ファイルは ` プロジェクトディレクトリ/.git/config `）
```
git config --local -l
```
現在のリポジトリのリモート接続状態のみを表示
```
git remote -v
```
## Linuxで公開鍵認証を用いる
### 環境設定
1. パブリック・キーをGitHubのWebサイトに登録する
1. プライベート・キーをローカルマシンに登録する
    1. ` /home/ユーザ/.ssh/config ` ファイルを次のように作成する  
```bash
Host github.com
        Hostname github.com
        User git
        IdentityFile ~/.ssh_key/id_rsa_github
```
    1. configのIdentityFile指定した先にプライベート・キーをコピーする  
この例では、`~/.ssh_key` というディレクトリを作成し、その中に秘密鍵ファイル `id_rsa_github` をコピーする
1. 接続テスト

```bash
ssh -T github.com
    Enter passphrase for key '/home/ユーザ名/.ssh_key/id_rsa_github':   
    Hi [GitHubユーザ名]! You've successfully authenticated, but GitHub does not provide shell access.
```

### 利用


## git diffでGUIツールを利用する
### LinuxでGUIツール Meld を使う方法
` .gitconfig ` に対して次の設定を記述する
```
[diff]
    tool = meld
[difftool "meld"]
    cmd = meld "$LOCAL" "$REMOTE"
[alias]
    meld = difftool -y -d -t meld
    guidiff = difftool -y -d -t meld
```
コマンドラインで利用する場合は  
`git meld [コミットID 1] [コミットID 2]` (aliasを使う例)  
`git difftool -y -d -t meld [コミットID 1] [コミットID 2]`

### WindowsでGUIツール WinMerge を使う方法
` .gitconfig ` に対して次の設定を記述する
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

| 引数 | 説明 |
| --- | --- |
| -e | ESCキーでWinMergeを閉じる |
| -f *.cpp | *.cppファイルのみ対象とする |
| -r | サブディレクトリをすべて対象とする |
| -s | 複数のウィンドウを開かず、シングル・インスタンスで実行する |
| -u | 最近使用した項目に追加しない |
| -wl, -wr | 左, 右の表示部を読み取り専用として開く |

git-diffのコマンドラインオプション

| 引数 | 引数(Long) | 説明 |
| --- | --- | --- |
| -d | --dir-diff | ディレクトリ差分を実行 |
| -y | --no-prompt | diffツールを表示する前にプロンプトを出さない |
| -t ツール | --tool=ツール | 指定したdiffツールを利用する |

コマンドラインより、` git windiff コミットNo1 コミットNo2 `（aliasを用いる場合）を実行する

