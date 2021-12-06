# GitHub Pages で webページ公開方法
## リポジトリとWebページを分離する
ソースコード等が公開されるGitHubリポジトリと、Webページで公開するhtmlファイル等は、ブランチとディレクトリで分離することが推奨されている。

```
[main]ブランチ
   |
   +-- [root] dir
           +-- file1.cpp
           +-- file2.py
[gh-pages]ブランチ
   |
   +-- [root] dir
           +-- [docs] dir
                   +-- index.html
                   +-- styles.css
```

gh-pages ブランチを新規作成し、ワーキングブランチを切り替え。

```
$ git branch gh-pages
$ git checkout gh-pages
```

ルートディレクトリ直下に docs というディレクトリを作成。docsをWeb公開フォルダに指定する。

```
$ mkdir docs
$ cd docs
$ vim index.html
```

コミットし、GitHub.comにアップロードする

```
$ cd ..
$ git add docs
$ git commit -m "Webページ作成"
$ git push origin gh-pages
```

GitHub.comの管理ページより、「設定（Settings）」を開く

![githubpage-fig-01](https://user-images.githubusercontent.com/11648452/144861446-b34b9272-88f0-4248-bb71-88a4250a478a.jpg)

Pages設定の中のSource設定項目で、ドロップダウンリストよりブランチ名（gh-pages）・ディレクトリ名（docs）を選択し、Saveボタンを押して設定を完了する。

![githubpage-fig-02](https://user-images.githubusercontent.com/11648452/144862277-05e8f373-78db-4231-a5a9-91c538d18f84.jpg)
