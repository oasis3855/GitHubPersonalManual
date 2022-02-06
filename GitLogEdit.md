# Gitリポジトリの履歴（コミット日時、メッセージ）編集

<br />
<br />

- [Gitリポジトリの履歴（コミット日時、メッセージ）編集](#gitリポジトリの履歴コミット日時メッセージ編集)
  - [コミット日時の修正編集](#コミット日時の修正編集)
    - [指定した１つのコミットのコミット日時を修正](#指定した１つのコミットのコミット日時を修正)
    - [指定した複数のコミットのコミット日時を修正](#指定した複数のコミットのコミット日時を修正)
    - [最初のコミット（first commit）も修正対象とする場合](#最初のコミットfirst-commitも修正対象とする場合)
      - [filter-branch を用いて強引に書き換える方法](#filter-branch-を用いて強引に書き換える方法)
      - [first commitのamendみ、環境変数 GIT_COMMITTER_DATE を用いる方法](#first-commitのamendみ環境変数-git_committer_date-を用いる方法)
  - [コミット メッセージの修正編集](#コミット-メッセージの修正編集)

<br />
<br />

## コミット日時の修正編集

修正前の状態

<pre>
$ git log --pretty="%h %aI %cI %d" --graph

<i>> * 47bba95 2022-02-06T14:39:32+09:00 2022-02-06T14:39:32+09:00  (HEAD -> master)
> * 1bc3299 2022-02-06T14:39:18+09:00 2022-02-06T14:39:18+09:00 
> * 51086cd 2022-02-06T14:38:58+09:00 2022-02-06T14:38:58+09:00 </i>
</pre>

### 指定した１つのコミットのコミット日時を修正

修正するコミットの1つ前のコミットIDを指定して、git rebase を実行。コミットIDを指定する他に、HEADからの世代を指定してもよい

```
$ git rebase -i 51086cd
```
または
```
$ git rebase -i HEAD~2
```

対話モード（-i）の場合、nanoエディタで次の画面が表示されるので、修正するコミットの「pick」を「edit」に書き換える。（今回は、1bc3299 version 2のコミットを書き換えることとする

<pre>
<b></em>edit</em></b> 1bc3299 version 2
pick 47bba95 version 3

 Rebase 51086cd..47bba95 onto 51086cd (2 commands)

 Commands:
 p, pick = use commit
 r, reword = use commit, but edit the commit message
 e, edit = use commit, but stop for amending
 s, squash = use commit, but meld into previous commit
 f, fixup = like "squash", but discard this commit's log message
 x, exec = run command (the rest of the line) using shell
 d, drop = remove commit

 These lines can be re-ordered; they are executed from top to bottom.

 If you remove a line here THAT COMMIT WILL BE LOST.

 However, if you remove everything, the rebase will be aborted.
</pre>

保存（Ctrl - O）、終了（Ctrl - X）すると、確認メッセージが表示される。「stopped at 1bc3299 ...」と、修正ポインタが置かれている地点がメッセージ表示されている

<pre>
> Stopped at 1bc3299...  version 2
> You can amend the commit now, with
>
>   git commit --amend 
>
> Once you are satisfied with your changes, run
>
>   git rebase --continue
</pre>

日付（Author Date）を修正する

```
$ git commit --amend --date="1998-05-07T02:00:00+09:00"
```

修正を確定し、次の処理へ

```
$ git rebase --continue
```

この状態では、まだCommit Dateのほうが変更されていない

<pre>
$ git log --pretty="%h %aI %cI %d" --graph

><i> * dc63d5b 2022-02-06T14:39:32+09:00 2022-02-06T19:06:33+09:00  (HEAD -> master)
> * 70bc31d <b>1998-05-07T02:00:00+09:00 2022-02-06T19:06:13+09:00</b> 
> * 51086cd 2022-02-06T14:38:58+09:00 2022-02-06T14:38:58+09:00 </i>
</pre>

Commit Date を Author Date に書き換える。書き換えたいコミットの１つ前のコミットIDを指定して git rebase を実行する

```
$ git rebase 51086cd --committer-date-is-author-date
```

修正が全て終わった状況

<pre>
$ git log --pretty="%h %aI %cI %d" --graph

<i>> * 5af3dcc 2022-02-06T14:39:32+09:00 2022-02-06T14:39:32+09:00  (HEAD -> master)
> * 70bcdbb 1998-05-07T02:00:00+09:00 1998-05-07T02:00:00+09:00 
> * 51086cd 2022-02-06T14:38:58+09:00 2022-02-06T14:38:58+09:00 </i>
</pre>

### 指定した複数のコミットのコミット日時を修正

修正するコミットの１つ前のコミットIDを指定して、git rebase を実行

```
$ git rebase -i 51086cd
```

nanoエディタで次の画面が表示されるので、修正するコミットの「pick」を「edit」に書き換える。

<pre>
<b></em>edit</em></b> 1bc3299 version 2
<b></em>edit</em></b> 47bba95 version 3
</pre>

コミット２つ分の日付を連続修正・確定する。修正は古いものから順に行われるので、画面表示されるコミット メッセージに注意して、修正ポインタの位置を確認しながら作業すること

```
$ git commit --amend --date="1998-05-05T01:00:00+09:00"
$ git rebase --continue
$ git commit --amend --date="1998-05-07T02:00:00+09:00"
$ git rebase --continue
```

Commit Date を Author Date に書き換える。書き換えたいコミットの１つ前のコミットIDを指定して git rebase を実行する

```
$ git rebase 51086cd --committer-date-is-author-date
```

### 最初のコミット（first commit）も修正対象とする場合

#### filter-branch を用いて強引に書き換える方法

コミットIDやHEADからの世代を指定して git rebase -i を行っても、最初のコミットを修正対象とすることは出来ない

最初のコミットを修正対象とする場合は…

```
$ git rebase -i --root
```

nanoエディタで次の画面が表示されるので、修正するコミットの「pick」を「edit」に書き換える。今回の例では、全てのコミットを修正対象としてみる


```
edit 51086cd version 1
edit 1bc3299 version 2
edit 47bba95 version 3
```

コミットの日付を連続修正・確定する。修正は古いものから順に行われるので、画面表示されるコミット メッセージに注意して、修正ポインタの位置を確認しながら作業すること

```
$ git commit --amend --date="1998-05-03T00:00:00+09:00"
$ git rebase --continue
$ git commit --amend --date="1998-05-05T01:00:00+09:00"
$ git rebase --continue
$ git commit --amend --date="1998-05-07T02:00:00+09:00"
$ git rebase --continue
```

Commit Date を Author Date に書き換える。書き換えたいコミットの１つ前のコミットIDを指定して git rebase を実行するのだが、最初のコミット（first commit）の前の指定など出来ないため、次のようなトリックを用いる

```
$ git filter-branch --env-filter 'export GIT_COMMITTER_DATE="$GIT_AUTHOR_DATE"'
```

修正が意図したとおりとなっているか、ログを表示する

<pre>
$ git log --pretty="%h %aI %cI %d" --graph

> * b7cb2eb 1998-05-07T02:00:00+09:00 1998-05-07T02:00:00+09:00  (HEAD -> master)
> * 2e52a98 1998-05-05T01:00:00+09:00 1998-05-05T01:00:00+09:00 
> * 7d9abba 1998-05-03T00:00:00+09:00 1998-05-03T00:00:00+09:00 
</pre>

#### first commitのamendみ、環境変数 GIT_COMMITTER_DATE を用いる方法

```
$ git rebase -i --root
```

nanoエディタで次の画面が表示されるので、修正するコミットの「pick」を「edit」に書き換える。今回の例では、全てのコミットを修正対象としてみる


```
edit 51086cd version 1
edit 1bc3299 version 2
edit 47bba95 version 3
```

コミットの日付を連続修正・確定する。修正は古いものから順に行われるので、画面表示されるコミット メッセージに注意して、修正ポインタの位置を確認しながら作業すること

first commitのみ、commit dateを環境変数で指定して同時書き換えする

```
$ GIT_COMMITTER_DATE="1998-05-03T00:00:00+09:00" git commit --amend --date="1998-05-03T00:00:00+09:00"
$ git rebase --continue
$ git commit --amend --date="1998-05-05T01:00:00+09:00"
$ git rebase --continue
$ git commit --amend --date="1998-05-07T02:00:00+09:00"
$ git rebase --continue
```

状況表示

<pre>
$ git log --pretty="%h %aI %cI %d" --graph

<i>> * a1491a4 1998-05-07T02:00:00+09:00 2022-02-06T16:29:57+09:00  (HEAD)
> * e320a73 1998-05-05T01:00:00+09:00 2022-02-06T16:29:39+09:00 
> * 7d9abba 1998-05-03T00:00:00+09:00 1998-05-03T00:00:00+09:00 </i>
</pre>

first commitの次から最新のコミットまで、Commit Date を Author Date に書き換える。書き換えたいコミットの１つ前のコミットIDを指定して git rebase を実行する

```
$ git rebase 7d9abba --committer-date-is-author-date
```

## コミット メッセージの修正編集

```
git rebase -i [1つ前のコミットID | --root]
```

nanoエディタで次の画面が表示されるので、修正するコミットの「pick」を「reword」に書き換える


```
reword 51086cd version 1
reword 1bc3299 version 2
pick 47bba95 version 3
```

保存（Ctrl - O）、終了（Ctrl - X）で修正対象の指定画面を保存しエディターを終了すると、続けてコメント文字列の編集画面が表示される。

古いコミットから順に編集画面が表示されるので、内容を書き換えた後、保存（Ctrl - O）、終了（Ctrl - X）でコメントが変更される。

