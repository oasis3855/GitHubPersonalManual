# Git環境設定

## 設定ファイル(.gitconfig)の作成
### .gitconfigの格納場所
システム設定のための `.gitconfig` ファイルは次の場所に作成される
- Linuxの場合 : `/etc/gitconfig`
- Windowsの場合 : `C:\Program Files\Git\etc\gitconfig`
グローバル設定のための `.gitconfig` ファイルはユーザディレクトリ直下に作成する
- Linuxの場合 : `/home/ユーザ/.gitconfig`
- Windowsの場合 : `C:\Users\ユーザ\.gitconfig`

### 状態表示
設定一覧の表示。`--system`（システム設定）, `--global`（グローバル設定）, `--local`（リポジトリ個別設定）のそれぞれ限定して表示することもできる。`--show-origin`を指定すると、それぞれの設定値を格納しているgitconfigファイルのフルパス名が併記される
```
git config [--system|--global|--local] --list|-l [--show-origin]
```
現在のリポジトリのリモート接続状態のみを表示
```
git remote -v
```

### .gitconfigの例
`/home/ユーザ/.gitconfig`の例。それぞれのセクションの設定については、[Git のカスタマイズ - Git の設定](https://git-scm.com/book/ja/v2/Git-%E3%81%AE%E3%82%AB%E3%82%B9%E3%82%BF%E3%83%9E%E3%82%A4%E3%82%BA-Git-%E3%81%AE%E8%A8%AD%E5%AE%9A) (Git公式マニュアル)を参照
```
[core]
    pager = less
    # 対話形エディター（commitメッセージの編集 等）
    editor = vim
    # utf8以外をクオートしないよう設定
    #（ShiftJISのファイル名をコマンドラインに表示できるようにする）
    quotepath = false
[user]
    name = [logに表示するユーザ名]
    email = [メールアドレス@users.noreply.github.com]
[code]
    # Visual Studio Code
    editor = code --wait
[merge]
    # Visual Studio Code
    tool = code --wait \"$MERGED\"
[diff]
    tool = meld
[difftool "meld"]
    cmd = meld "$LOCAL" "$REMOTE"
[alias]
    meld = difftool -y -d -t meld
    guidiff = difftool -y -d -t meld
    log1 = log --pretty=format:'%C(yellow)%h%Creset %C(green)%cd%Creset %s %C(red)%d%Creset' --date=format:'%Y/%m/%d %H:%M' --graph --all
```


## 公開鍵認証を用いる
### 環境設定
- パブリック・キーをGitHubのWebサイトに登録する
- プライベート・キーをローカルマシンに登録する

 ` /home/ユーザ/.ssh/config ` ファイルを次のように作成し、そこで指定したディレクトリにプライベート・キー（ここでの例：id_rsa_github）を格納する
```
Host github.com
        Hostname github.com
        User git
        IdentityFile ~/.ssh_key/id_rsa_github
```
- 接続テスト

```
ssh -T github.com
>    Enter passphrase for key '/home/ユーザ名/.ssh_key/id_rsa_github':   
>    Hi [GitHubユーザ名]! You've successfully authenticated, but GitHub does not provide shell access.
```


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

