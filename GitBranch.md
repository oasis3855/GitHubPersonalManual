# Git ブランチを使った作業メモ

ブランチの一覧を表示する。` --list `オプションは付けなくても一覧表示できる。` -v `オプションは直近のコミット文字列を表示する。出力されたリストで * がついているものが、現在チェックアウトしているブランチ
```
git branch [--list]
git branch -v
```

新しいブランチを作成する
```
git branch 新ブランチ名
```

ブランチの名前を変更する
```
git branch -m 変更前ブランチ名 変更後ブランチ名
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

