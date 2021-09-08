# Git コマンドラインの使い方メモ

- [Git コマンドラインの使い方メモ](#git-コマンドラインの使い方メモ)
  - [ローカル リポジトリに対する操作コマンド](#ローカル-リポジトリに対する操作コマンド)
    - [ステージング（コミット対象リストに登録する）](#ステージングコミット対象リストに登録する)
    - [コミット](#コミット)
    - [ログの表示](#ログの表示)
    - [差分表示・ファイルの表示](#差分表示ファイルの表示)
    - [過去のコミット時点に戻すresetの方法](#過去のコミット時点に戻すresetの方法)
    - [一時退避、復元](#一時退避復元)
  - [リモート リポジトリに対する操作コマンド](#リモート-リポジトリに対する操作コマンド)

## ローカル リポジトリに対する操作コマンド
状況表示
```
git status
```
現在のHEADが存在しているブランチ名
```
git rev-parse --abbrev-ref HEAD
```
最新のコミットID（ハッシュ値）を短い形式で表示
```
git rev-parse --short HEAD
```
***
### ステージング（コミット対象リストに登録する）
ファイルやディレクトリをコミット対象に登録する（ステージング）
```
git add [filename 1] [filename 2] ...
```
変更のあったファイルのみをコミット対象に登録する（--update/-u）
```
git add -u
```
カレントディレクトリ内のすべてのファイルをコミット対象に登録する（--all/-A）
```
git add -A
git add .
```
実際に登録せず、どのファイルが対象となるかだけ表示する（--dry-run/-n）
```
git add -n -u
```
インタラクティブ モードでファイルを選択してコミット対象に登録する（--interactive/-i）（[manページ](https://git-scm.com/book/ja/v2/Git-%E3%81%AE%E3%81%95%E3%81%BE%E3%81%96%E3%81%BE%E3%81%AA%E3%83%84%E3%83%BC%E3%83%AB-%E5%AF%BE%E8%A9%B1%E7%9A%84%E3%81%AA%E3%82%B9%E3%83%86%E3%83%BC%E3%82%B8%E3%83%B3%E3%82%B0)）
```
git add -i
```
コミット対象への登録状況を画面表示する
```
git add -v
```
addしたファイルを取り消す（ファイル名を指定しなければ、すべてのaddしたファイルを取り消す）
```
git reset [path/to/filename]
```

***
### コミット
コミットする。 -m オプションでコミット メッセージを指定すると、対話エディタを起動せずコミットできる
```
git commit [-m "コミット メッセージ 文字列"]
```
直前のコミット メッセージを再編集する
```
git commit --amend [-m "コミット メッセージ 文字列"]
```

***
### ログの表示
コミット ログの表示（特定のリモート名,ブランチ名,ファイル名などを指定することもできる）
```
git log [リモート名] [リモート名/ブランチ名] [ブランチ名] [ファイル名]
```
コミット ログの表示をカスタマイズする
```
git log -n 5
git log -5
git log --oneline --shortstat --name-status --abbrev-commit
```
コミット ログの表示をformatでカスタマイズする
```
git log --pretty=format:'%C(yellow)%h%Creset %C(green)%cd%Creset %s %C(red)%d%Creset' --date=format:'%Y/%m/%d %H:%M' --graph
```
コミット ログの表示（特定のファイル名を指定し、ファイル名変更の追跡も行う）
```
git log --follow [filename]
```
git log の主なオプション（[manページ](https://git-scm.com/book/ja/v2/Git-%E3%81%AE%E5%9F%BA%E6%9C%AC-%E3%82%B3%E3%83%9F%E3%83%83%E3%83%88%E5%B1%A5%E6%AD%B4%E3%81%AE%E9%96%B2%E8%A6%A7)）

| 引数 | 説明 |
| --- | --- |
| --oneline | 各コミットを一行で出力（--pretty=oneline --abbrev-commit と同じ） |
| --stat | 各コミットで変更されたファイルの統計情報を表示する |
| --shortstat | --stat コマンドのうち、変更/追加/削除 の行だけを表示する |
| --name-status | 変更されたファイルと 追加/修正/削除 情報を表示する |
| --abbrev-commit | コミットのハッシュを短く表示する |
| --graph | ブランチやマージを分かりやすくアスキー・グラフで表示 |
| --pretty=format:'...' | 別のフォーマットで表示する |
| -n 数値 , -数値 | 表示するコミットの個数を指定（--max-count=数値 も同じ意味） |
| --since=日付 | 指定した日以降のコミットに限定（--since=2021/8/16） |
| --before=日付 | 指定した日以前のコミットに限定 |
| --merges | マージした時のコミットを表示 |
| --first-parent | 最初に親となったコミットを表示する（マージコミットのマージ元を除外する） |
| --all, --branches | 全てのブランチを表示 |
| --graph | ブランチ ツリーをASCII文字線で表現する |


--pretty=format:'...' の主なオプション（[manページ](https://git-scm.com/docs/pretty-formats)）

| プレースホルダー | 説明 |
| --- | --- |
| %H | コミットのハッシュ値（40文字） |
| %h | コミットのハッシュ値（短い形式） |
| %P | 親コミットのハッシュ値（40文字） |
| %p | 親コミットのハッシュ値（短い形式） |
| %ai | addの日時 |
| %ad | addの日時（--date=format:'%Y/%m/%d %H:%M:%S' の書式を用いる） |
| %an | addしたユーザの名前 |
| %ci | コミットした日時 |
| %cd | コミットの日時（--date=format:'%Y/%m/%d %H:%M:%S' の書式を用いる） |
| %cn | コミットしたユーザの名前 |
| %s | コミット時のメッセージ文字列 |
| %C(...) | 文字列の着色開始（red, green, cyan, yellow 等を指定） |
| %Creset | 文字列の着色終了 |

***
### 差分表示・ファイルの表示
差分表示（特定のコミットIDや、特定のファイル名、ディレクトリを指定することもできる）
```
git diff [コミットID 1] [コミットID 2] [filename]
git diff HEAD^         ← 最新（最後）のコミットでの変更点を表示
```
差分の概要だけを表示
```
git diff --stat [コミットID 1] [コミットID 2]
```
特定のコミットIDのときまで巻き戻して、ファイルを表示する（
```
git show [コミットID]:[ファイル名]
git show [コミットID]:[ファイル名] | gedit -
```

```HEAD```の記述方法  
``` ^ ``` はマージ前の複数の親を指定できる、``` ~ ``` は直線的に1番目の親だけを遡る。ブランチが一切無く直線的なコミットヒストリーの場合は ``` ^ ``` と ``` ~ ``` は単に○世代前を意味するだけになる。

|  コード | コード | 複数の親が無い | 複数の親が存在 |
| --- | --- | --- | --- |
| HEAD | | 最新（現在）のコミット | |
| HEAD~ | HEAD^, HEAD^1 | 1世代前 | 1番目の親 |
| | HEAD^2 | (1世代前) | 2番めの親 |
| HEAD~~ , HEAD~2 | HEAD^^ , HEAD^1^1 | 2世代前 | 1番目の親の1番目の親 |
| | HEAD^2^ , HEAD^2^1 | (2世代前) | 2番目の親の1番目の親 |
| HEAD~~~ , HEAD~3 | HEAD^^^ , HEAD^1^1^1 | 3世代前 | 1番目の親の1番目の親の1番目の親 |

***
### 過去のコミット時点に戻すresetの方法
コミットを取り消す（commit のみを取り消す）。[コミットID]を指定して戻ることや、`HEAD^`を指定して、1回前のコミット時点まで戻る（`HEAD^1`の1は省略でき`HEAD^`と同じ。また2回前は`HEAD^^`や`HEAD~~`や`HEAD~2`と記述）。ブランチが指し示すHAEDが移動するだけなので、変更されたファイルは現状のまま
```
git reset --soft [コミットID]/HEAD^/HEAD~n
```
コミットを取り消す（add と commit を取り消す）。`--mixed`オプションはデフォルトなので、記述しなくても良い。ブランチが指し示すHAEDとindexが移動するだけなので、変更されたファイルは現状のまま
```
git reset [コミットID]/HEAD^/HEAD~n
git reset --mixed [コミットID]/HEAD^/HEAD~n
```
コミットを取り消す（add と commit を取り消し、変更されているファイルも編集前に戻される）。`HEAD`を指定すると、直前のコミット後のファイル編集を全て取り消すことが出来る
```
git reset --hard [コミットID]/HEAD/HEAD^/HEAD~n
```
HEADの位置ログを表示する
```
git reflog
```
HEADの位置ログを元にブランチ状態を元に戻す。（reflogで表示される`HEAD@{n}`を指定する）
```
git reset --hard HEAD@{n}
```
コミットやインデックスの戻し方の例
- ファイルを編集し、`git add -u`を行った。`add`だけをなかったことにしたい（インデックスはHEAD位置に戻す）
```
git reset --mixed HEAD
```
- ファイルを編集し、`git add -u`を行った。`add`を取り消し、ファイル編集もなかったことにしたい（インデックスはHEAD位置に戻す）
```
git reset --hard HEAD
```
- `git reset`の実行を取り消したい
```
git reflog
    4d27e78 (HEAD -> main) HEAD@{0}: reset: moving to HEAD
    4d27e78 (HEAD -> main) HEAD@{1}: commit: test1
    da5ca9b (origin/main, origin/HEAD) HEAD@{2}: reset: moving to HEAD  ← ここに戻る
    …
git reset --hard HEAD@{2}
```

***
### 一時退避、復元
HEAD状態以降に変更・ステージングされた内容を一時的に退避し、HEADの状態に戻す。-u オプションを付けると、まだaddされていない未追跡ファイル（新規ファイル）も含めて退避する
```
git stash [-u]
```
退避リスト（状況）を表示
```
git stash list
```
退避したものを元に戻す。` git list `で複数の退避状態がある場合、` stash@{n} `の状態に戻すことが出来る。` stash@{n} `を付けなければ、直近に退避した状態に戻す。` --index `を付けると、ステージング状態（add状態）かどうかも戻す。` --index `を付けなければ、ステージング状態は全てリセット（何もしていない状態）される
```
git stash apply [stash@{n}] [--index]
```
退避リストを全て消去する
```
git stash clear
```
applyとclearを同時にセットで行う
```
git stash pop [stash@{n}]
```

## リモート リポジトリに対する操作コマンド
リモートリポジトリを、ローカルディスクにクローン
```
git clone git@github.com:USER_NAME/REPOSITORY_NAME.git
```
リモートリポジトリと、ローカルリポジトリの紐づけ
```
git remote add origin git@github.com:USER_NAME/REPOSITORY_NAME.git
```
リモートリポジトリと、ローカルリポジトリの紐づけ状況を画面表示する
```
git remote -v
git remote show -n origin
```
リモートリポジトリ（origin）から指定したブランチ（main）を取得し反映する（デフォルトブランチ名がmainなのかmasterなのか注意すること）。  
なお、下記例の[特定ブランチ名]を省略すればすべてのブランチが取得される。また、[リモート名]を省略すれば、デフォルトで設定されたリモートリポジトリ（デフォルトではorigin）に接続される。
```
git pull [リモート名] [特定ブランチ名]
git pull origin main
```
または、ローカルにマージする前に変更内容を確認したい場合は、次の2段階のコマンドを使う。  
fetchの機能は  
- リモートリポジトリ（origin）の特定ブランチ（main） → ローカルにある「リモートの変更点をキャッシュしている」ブランチ（origin/main）  

mergeの機能は
- ローカルのorigin/mainブランチ → ローカルのmainブランチ  

```
git fetch [リモート名] [特定ブランチ名]
git fetch origin main
 **** ここで差分確認等を行う（例 : git log origin --oneline , git diff --stat main origin/main）
git merge origin/main
```
mergeでエラーが出た場合（error: Your local changes to the following files would be overwritten by merge）で、強制mergeしてリモートの内容で全て上書きしたい時
```
git fetch origin main
git reset --hard origin/main
```
リモートリポジトリoriginのコミットログを10件前まで（-n 10）、簡潔に1行表示（--oneline）で表示する
```
git log origin -n 10 --oneline
```
ローカルリポジトリのmainブランチと、リモートリポジトリoriginのmainブランチの差分を確認する。--statオプションを付けると、変更したファイル名と変更割合のバーグラフを表示。 --name-statusオプションは、変更したファイル名のみを表示する。
```
git diff main origin/main
git diff --stat main origin/main
git diff --name-status main origin/main
```
ローカルリポジトリの変更をリモートに反映する
```
git push origin main
```
コミットしているが、プッシュしていないものがないか確認する
```
git log origin/main..main
```
git pushを取り消す（直前のコミットを取り消し、強制pushを行う）
```
git reset --hard HEAD^
git push -f origin main
```

