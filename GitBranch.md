# Git ブランチを使った作業メモ

ブランチの一覧を表示する。` --list `オプションは付けなくても一覧表示できる。` -v `オプションは直近のコミット文字列を表示する。` -a `オプションは、リモートも含めて一覧表示する。出力されたリストで * がついているものが、現在チェックアウトしているブランチ
```
git branch [--list]
git branch -v
git branch -a
```

新しいブランチを作成する。オプション` -b `を付けると、作成後すぐにそのブランチに切り替える（`git checkout ブランチ名`）操作を同時に行える
```
git branch [-b] 新ブランチ名
```

ブランチの名前を変更する
```
git branch -m 変更前ブランチ名 変更後ブランチ名
git branch --move 変更前ブランチ名 変更後ブランチ名
```

ブランチの切り替え。切り替えを行うと、HEADがこのブランチを指し示すようになる
```
git checkout ブランチ名
```

ブランチ ツリーをASCII文字線で表現したコミット ログを表示する
```
git log --oneline -graph
```

mainに派生ブランチをマージする。マージ後に派生ブランチを削除する。**必ず残存するブランチ（この場合はmain）に切り替えて（checkout）から実行すること**
```
git checkout main
git merge 派生ブランチ名
git branch -d 派生ブランチ名
```

mainにマージされて不要となったブランチ一覧を表示する。ここで表示されたブランチは削除（` git branch -d `）できる
```
git checkout main
git branch --merged
```

まだマージされていないブランチ一覧を表示する。ここで表示されたブランチは通常削除（` git branch -d `）しようとすると、エラーが発生する
```
git branch --no-merged
```

ブランチを強制削除する（` git branch -d ` で削除できなかったブランチでも削除できる）
```
git branch -D ブランチ名
```

# リモート リポジトリに対する操作コマンド
リモートのブランチ一覧を表示。` -r `オプションはリモートのみを表示対象。 ` -a `オプションはローカルとリモートの双方を対象
```
git branch -r
git branch -a
```
`git clone`はmainブランチだけをクローンするが、それ以外のブランチを更にクローンする。` git checkout --track origin/ブランチ名 ` は ` git checkout -b ブランチ名 origin/ブランチ名 ` の短縮形
```
git clone git@github.com:USER_NAME/REPOSITORY_NAME.git
cd REPOSITORY_NAME
git checkout --track origin/ブランチ名
```
すでにローカルにダウンロード済みのリポジトリが最新かどうかチェックし、最新でなければリモートからダウンロードしてマージする一連の処理
```
git checkout ブランチ名
git fetch [origin ブランチ名]
git log [origin] --oneline --all --graph      ← ログ確認などを行う
git merge origin/ブランチ名
```
指定したブランチをリモートにプッシュする。プッシュ先のブランチ名は` HEAD `または` 現在のブランチ名 `のどちらでもよい
```
git checkout ブランチ名
git push [origin HEAD]
```
リモートのブランチを削除する
```
git push origin --delete ブランチ名
```

