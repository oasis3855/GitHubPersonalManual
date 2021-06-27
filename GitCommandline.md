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
コミット ログの表示（特定のファイル名を指定することもできる）
```
git log [filename]
```
コミット ログの表示（特定のファイル名を指定し、ファイル名変更の追跡も行う）
```
git log --follow [filename]
```
差分表示（特定のコミットIDや、特定のファイル名、ディレクトリを指定することもできる）
```
git diff [コミットID 1] [コミットID 2] [filename]
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
リモートリポジトリの変更をローカルに反映する（デフォルトブランチ名がmainなのかmasterなのか注意すること）
```
git pull origin main(master)
```
または、ローカルにマージする前に変更内容を確認したい場合は、次の2段階のコマンドを使う
```
git fetch origin
git merge origin/master
```
※ローカル都の差分を確認する
```
git diff master origin/master
```
ローカルリポジトリの変更をリモートに反映る（デフォルトブランチ名がmainなのかmasterなのか注意すること）
```
git push origin main(master)
```

