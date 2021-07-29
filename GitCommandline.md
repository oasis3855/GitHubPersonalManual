# Git コマンドラインの使い方メモ

## ローカル リポジトリに対する操作コマンド
状況表示
```
git status
```
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
```
コミット
```
git commit
```
メッセージ文字列をコマンドラインで指定してコミットする（対話エディタを起動しない）
```
git commit -m "コミット メッセージ 文字列"
```
コミットを取り消す（add と commit を取り消す）
```
git reset
```
コミットを取り消す（commit のみを取り消す）ソフトリセット
```
git reset --hard
```
コミットを取り消す（add と commit を取り消し、変更されているファイルも編集前に戻される）ハードリセット
```
git reset --hard
```
コミット ログの表示（特定のリモート名,ブランチ名,ファイル名などを指定することもできる）
```
git log [リモート名] [リモート名/ブランチ名] [ブランチ名] [ファイル名]
```
コミット ログの表示をカスタマイズする。表示件数の指定（-n 5 または -5）、簡潔に1行表示（--oneline）、変更ファイル数や行数を簡潔に表示（--shortstat）、変更したファイル名のみ出力（--name-status）、コミットIDを短い形式で表示（--abbrev-commit）
```
git log -n 5
git log -5
git log --oneline --shortstat --name-status --abbrev-commit
```
コミット ログの表示（特定のファイル名を指定し、ファイル名変更の追跡も行う）
```
git log --follow [filename]
```
差分表示（特定のコミットIDや、特定のファイル名、ディレクトリを指定することもできる）
```
git diff [コミットID 1] [コミットID 2] [filename]
git diff HEAD^         ← 最新（最後）のコミットでの変更点を表示
```
差分の概要だけを表示
```
git diff --stat [コミットID 1] [コミットID 2]
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
```
リモートリポジトリ（origin）から指定したブランチ（main）を取得し反映する（デフォルトブランチ名がmainなのかmasterなのか注意すること）。  
なお、下記例の[特定ブランチ名]を省略すればすべてのブランチが取得される。また、[リモート名]を省略すれば、デフォルトで設定されたリモートリポジトリ（デフォルトではorigin）に接続される。
```
git pull [リモート名] [特定ブランチ名]
git pull origin main
git pull origin master
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
ローカルリポジトリの変更をリモートに反映る（デフォルトブランチ名がmainなのかmasterなのか注意すること）
```
git push origin main(master)
```

