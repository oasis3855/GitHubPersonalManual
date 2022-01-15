# リポジトリの部分抽出・結合の作業例

- [リポジトリの部分抽出・結合の作業例](#リポジトリの部分抽出結合の作業例)
  - [リポジトリの一部分を抽出](#リポジトリの一部分を抽出)
    - [（１）特定ディレクトリのみを残す](#１特定ディレクトリのみを残す)
    - [（２）指定したファイルやディレクトリを削除する](#２指定したファイルやディレクトリを削除する)
  - [2つのリポジトリを1つに結合した新しいリポジトリを作成する](#2つのリポジトリを1つに結合した新しいリポジトリを作成する)
  - [2つのローカル リポジトリを結合する](#2つのローカル-リポジトリを結合する)
  - [別のリポジトリを、指定のディレクトリ下に結合する](#別のリポジトリを指定のディレクトリ下に結合する)
  - [ファイルの履歴を追跡できる形で、別のリポジトリを指定ディレクトリ以下にコピーする](#ファイルの履歴を追跡できる形で別のリポジトリを指定ディレクトリ以下にコピーする)

## リポジトリの一部分を抽出
### （１）特定ディレクトリのみを残す

GitHubからリポジトリをクローンする
```
$ git clone git@github.com:oasis3855/test01.git
> Cloning into 'test01'...
> Enter passphrase for key '/home/vm/.ssh_key/id_rsa_github':
> remote: Enumerating objects: 11, done.
> remote: Counting objects: 100% (11/11), done.
> remote: Compressing objects: 100% (8/8), done.
> remote: Total 11 (delta 0), reused 11 (delta 0), pack-reused 0
> Receiving objects: 100% (11/11), done.
```

リポジトリのディレクトリに移動し、含まれるファイルを表示する
```
$ cd test01/

test01$ ls -1R --ignore=.*
> .:
> test01_hello.c
> test01_hello.py
> test01_subdir
> test01_subdir2
> 
> ./test01_subdir:
> test01_hello.pl
> 
> ./test01_subdir2:
> test01_hello.sh
```

コミット・ログとブランチ一覧を表示
```
test01$ git log --oneline
> a1d9ff5 (HEAD -> main, origin/main, origin/HEAD) create subdir2, modify py
> 6dca8db c, perl, python Hello World

test01$ git branch -a
> * main
>   remotes/origin/HEAD -> origin/main
>   remotes/origin/main
```

指定したディレクトリ「test01_subdir」のみを残して、他をすべて削除（ファイルも、コミットログも削除し、元から無かったことにする）
```
test01$ git filter-branch --prune-empty --subdirectory-filter ./test01_subdir
> WARNING: git-filter-branch has a glut of gotchas generating mangled history
>          rewrites.  Hit Ctrl-C before proceeding to abort, then use an
>          alternative filtering tool such as 'git filter-repo'
>          (https://github.com/newren/git-filter-repo/) instead.  See the
>          filter-branch manual page for more details; to squelch this warning,
>          set FILTER_BRANCH_SQUELCH_WARNING=1.
> Proceeding with filter-branch...
> 
> Rewrite 6dca8dbdc93ba5087d56b234af8d68bd5c094cca (1/1) (0 seconds passed, remaining 0 predicted)
> Ref 'refs/heads/main' was rewritten
```

ファイル一覧とコミット・ログを表示。指定したディレクトリ「test01_subdir」内のファイルが全てルートディレクトリに移動され、ほかのファイルはコミット・ログも含めすべて削除されているのが分かる。
```
test01$ ls -1R --ignore=.*
> .:
> test01_hello.pl

test01$ git log --oneline --stat
> 09e24e7 (HEAD -> main) c, perl, python Hello World
>  test01_hello.pl | 7 +++++++
>  1 file changed, 7 insertions(+)
```

### （２）指定したファイルやディレクトリを削除する

GitHubからリポジトリをクローンする
```
$ git clone git@github.com:oasis3855/test01.git
> Cloning into 'test01'...
> Enter passphrase for key '/home/vm/.ssh_key/id_rsa_github':
> remote: Enumerating objects: 11, done.
> remote: Counting objects: 100% (11/11), done.
> remote: Compressing objects: 100% (8/8), done.
> remote: Total 11 (delta 0), reused 11 (delta 0), pack-reused 0
> Receiving objects: 100% (11/11), done.
```

リポジトリのディレクトリに移動し、含まれるファイルを表示する
```
$ cd test01/

test01$ ls -1R --ignore=.*
> .:
> test01_hello.c
> test01_hello.py
> test01_subdir
> test01_subdir2
> 
> ./test01_subdir:
> test01_hello.pl
> 
> ./test01_subdir2:
> test01_hello.sh
```

コミット・ログとブランチ一覧を表示
```
test01$ git log --oneline
> a1d9ff5 (HEAD -> main, origin/main, origin/HEAD) create subdir2, modify py
> 6dca8db c, perl, python Hello World

test01$ git branch -a
> * main
>   remotes/origin/HEAD -> origin/main
>   remotes/origin/main
```

指定したディレクトリ「test01_subdir2」と、ファイル「test01_hello.c と test01_hello.py」を削除する。結果として（１）の例と同じく「test01_subdir」を残すことになる
```
test01$ git filter-branch --prune-empty --tree-filter 'rm -rf test01_subdir2 test01_hello.c test01_hello.py'
> WARNING: git-filter-branch has a glut of gotchas generating mangled history
>          rewrites.  Hit Ctrl-C before proceeding to abort, then use an
>          alternative filtering tool such as 'git filter-repo'
>          (https://github.com/newren/git-filter-repo/) instead.  See the
>          filter-branch manual page for more details; to squelch this warning,
>          set FILTER_BRANCH_SQUELCH_WARNING=1.
> Proceeding with filter-branch...
> 
> Rewrite a1d9ff58a86d4424ca1b605b37b2efdbdcbb12c1 (2/2) (0 seconds passed, remaining 0 predicted)
> Ref 'refs/heads/main' was rewritten
```

ファイル一覧とコミット・ログを表示。（１）と違い、ディレクトリ「test01_subdir」が残存している。削除指定したファイルはコミット・ログも含めすべて削除されているのが分かる。
```
/test01$ ls -1R --ignore=.*
> .:
> test01_subdir
> 
> ./test01_subdir:
> test01_hello.pl

test01$ git log --oneline --stat
> a1ada31 (HEAD -> main) c, perl, python Hello World
>  test01_subdir/test01_hello.pl | 7 +++++++
>  1 file changed, 7 insertions(+)
 ```

---

## 2つのリポジトリを1つに結合した新しいリポジトリを作成する

受け皿となる新しいリポジトリ「test」を作成し、初期化する
```
$ mkdir test

$ cd test

test$ git init
> Initialized empty Git repository in /home/vm/working_git/test/.git/
```

1つめのリポジトリ「test01」をダウンロードする
```
test$ git remote add test01 git@github.com:oasis3855/test01.git

test$ git fetch test01
> Enter passphrase for key '/home/vm/.ssh_key/id_rsa_github':
> remote: Enumerating objects: 11, done.
> remote: Counting objects: 100% (11/11), done.
> remote: Compressing objects: 100% (8/8), done.
> remote: Total 11 (delta 0), reused 11 (delta 0), pack-reused 0
> Unpacking objects: 100% (11/11), 1.05 KiB | 357.00 KiB/s, done.
> From github.com:oasis3855/test01
>  * [new branch]      main       -> test01/main
```

この時点でのファイル一覧とブランチ一覧を表示する。ワーキング・ブランチが指定されていないので、ファイルはまだ見えない状態
```
test$ ls -1R --ignore=.*
> .:

test$ git branch -a
>  remotes/test01/main
```

ブランチをチェックアウトし、ファイル一覧を確認する
```
test$ git checkout -b test01 test01/main
> Branch 'test01' set up to track remote branch 'main' from 'test01'.
> Switched to a new branch 'test01'

test$ git branch -a
> * test01
>   remotes/test01/main

test$ ls -1R --ignore=.*
> .:
> test01_hello.c
> test01_hello.py
> test01_subdir
> test01_subdir2
> 
> ./test01_subdir:
> test01_hello.pl
> 
> ./test01_subdir2:
> test01_hello.sh
```

ディレクトリ「test01_dir」を作成し、すべてのファイルをそこに移動する。git mv の -k オプションは、エラー（fatal: can not move directory into itself）を無視するため。エラーを出したくない場合は一つずつ移動してもよい
```
test$ mkdir test01_dir

test$ git mv -k * test01_dir

test$ git status
> ブランチ test01
> Your branch is up to date with 'test01/main'.
> 
> コミット予定の変更点:
>   (use "git restore --staged <file>..." to unstage)
>         renamed:    test01_hello.c -> test01_dir/test01_hello.c
>         renamed:    test01_hello.py -> test01_dir/test01_hello.py
>         renamed:    test01_subdir/test01_hello.pl -> test01_dir/test01_subdir/test01_hello.pl
>         renamed:    test01_subdir2/test01_hello.sh -> test01_dir/test01_subdir2/test01_hello.sh
```

コミットして git mv を確定する
```
test$ git commit -m "mkdir test01_dir"
> [test01 e6575dd] mkdir test01_dir
>  4 files changed, 0 insertions(+), 0 deletions(-)
>  rename test01_hello.c => test01_dir/test01_hello.c (100%)
>  rename test01_hello.py => test01_dir/test01_hello.py (100%)
>  rename {test01_subdir => test01_dir/test01_subdir}/test01_hello.pl (100%)
>  rename {test01_subdir2 => test01_dir/test01_subdir2}/test01_hello.sh (100%)

test$ git log --oneline --stat
> e6575dd (HEAD -> test01) mkdir test01_dir
>  test01_hello.c => test01_dir/test01_hello.c                   | 0
>  test01_hello.py => test01_dir/test01_hello.py                 | 0
>  {test01_subdir => test01_dir/test01_subdir}/test01_hello.pl   | 0
>  {test01_subdir2 => test01_dir/test01_subdir2}/test01_hello.sh | 0
>  4 files changed, 0 insertions(+), 0 deletions(-)
> a1d9ff5 (test01/main) create subdir2, modify py
>  test01_hello.py                | 2 ++
>  test01_subdir2/test01_hello.sh | 4 ++++
>  2 files changed, 6 insertions(+)
> 6dca8db c, perl, python Hello World
>  test01_hello.c                | 5 +++++
>  test01_hello.py               | 6 ++++++
>  test01_subdir/test01_hello.pl | 7 +++++++
>  3 files changed, 18 insertions(+)
```

2つめのリポジトリ「test02」をダウンロードする
```
test$ git remote add test02 git@github.com:oasis3855/test02.git

test$ git fetch test02
```

test02ブランチをチェックアウトする
```
test$ git branch -a
> * test01
>   remotes/test01/main
>   remotes/test02/main

test$ git checkout -b test02 test02/main

test$ git branch -a
>   test01
> * test02
>   remotes/test01/main
>   remotes/test02/main
```

ディレクトリ「test02_dir」を作成し、すべてのファイルをそこに移動する。ファイル一覧しっかり確認する点は、test01ブランチのファイルが混入していない点
```
test$ mkdir test02_dir

test$ git mv -k * test02_dir

test$ ls -1R --ignore=.*
> .:
> test02_dir
> 
> ./test02_dir:
> test02_hello.c
> test02_hello.py
> test02_subdir
> test02_subdir3
> 
> ./test02_dir/test02_subdir:
> test02_hello.pl
> 
> ./test02_dir/test02_subdir3:
> test02_hello.bat
```

コミットし確定する
```
test$ git commit -m "mkdir test02_dir"

test$ git log --oneline --stat
> 622a0cc (HEAD -> test02) mkdir test02_dir
>  test02_hello.c => test02_dir/test02_hello.c                    | 0
>  test02_hello.py => test02_dir/test02_hello.py                  | 0
>  {test02_subdir => test02_dir/test02_subdir}/test02_hello.pl    | 0
>  {test02_subdir3 => test02_dir/test02_subdir3}/test02_hello.bat | 0
>  4 files changed, 0 insertions(+), 0 deletions(-)
> 06dcfa5 (test02/main) add subdir3, modify c
>  test02_hello.c                  | 4 +++-
>  test02_subdir3/test02_hello.bat | 4 ++++
>  2 files changed, 7 insertions(+), 1 deletion(-)
> 889c0e2 create c,pl,py files
>  test02_hello.c                | 5 +++++
>  test02_hello.py               | 6 ++++++
>  test02_subdir/test02_hello.pl | 7 +++++++
>  3 files changed, 18 insertions(+)
```

現在のワーキング・ブランチ「test02」に「test01」をマージする
```
test$ git branch
>   test01
> * test02

test$ git branch --no-merged
>  test01

test$ git merge --allow-unrelated-histories test01
> Merge made by the 'recursive' strategy.
>  test01_dir/test01_hello.c                 | 5 +++++
>  test01_dir/test01_hello.py                | 8 ++++++++
>  test01_dir/test01_subdir/test01_hello.pl  | 7 +++++++
>  test01_dir/test01_subdir2/test01_hello.sh | 4 ++++
>  4 files changed, 24 insertions(+)
>  create mode 100644 test01_dir/test01_hello.c
>  create mode 100755 test01_dir/test01_hello.py
>  create mode 100755 test01_dir/test01_subdir/test01_hello.pl
>  create mode 100755 test01_dir/test01_subdir2/test01_hello.sh
```

マージされたことを確認（--no-merged オプションで表示されないことを確認）
```
test$ git branch --no-merged
```

コミット・ログ上でもマージされていることを確認
```
test$ git log --oneline --graph
> *   a7da5e3 (HEAD -> test02) Merge branch 'test01' into test02
> |\
> | * e6575dd (test01) mkdir test01_dir
> | * a1d9ff5 (test01/main) create subdir2, modify py
> | * 6dca8db c, perl, python Hello World
> * 622a0cc mkdir test02_dir
> * 06dcfa5 (test02/main) add subdir3, modify c
> * 889c0e2 create c,pl,py files
```

リモート接続を削除する
```
test$ git remote -v
> test01  git@github.com:oasis3855/test01.git (fetch)
> test01  git@github.com:oasis3855/test01.git (push)
> test02  git@github.com:oasis3855/test02.git (fetch)
> test02  git@github.com:oasis3855/test02.git (push)

test$ git remote rm test01

test$ git remote rm test02

test$ git remote -v
```

マージされた「test01」ブランチを削除する。（リモート接続を削除していない場合はエラー「error: The branch 'test01' is not fully merged」が出る）
```
test$ git branch -d test01
> Deleted branch test01 (was e6575dd).

test$ git branch -a
> * test02
```

残った「test02」ブランチを「main」に改名する
```
test$ git branch -m test02 main

test$ git branch -a
> * main
```

---

## 2つのローカル リポジトリを結合する

ローカルディスク内に、次のようにリポジトリが2つあるとする。
```
[dir]
  |
  +-- [repo_1]
  |     +- file_1.txt
  |
  +-- [repo_2]
        +- file_2.txt
```
repo_1 リポジトリに、repo_2 リポジトリの内容を取り込んでマージして、次のような形にする。
```
[dir]
  |
  +-- [repo_1]
        +- file_1.txt
        +- file_2.txt
```
---
作業手順

まず、repo_1 ディレクトリに入る

```
$ cd repo_1
``` 
次に、repo_1にrepo_2をリモート接続、取り込みを行う
```
repo_1$ git remote add remote_repo_2 ../repo_2
repo_1$ git fetch remote_repo_2
> warning: no common commits
> remote: Counting objects: 20, done.
> remote: Compressing objects: 100% (13/13), done.
> remote: Total 20 (delta 7), reused 0 (delta 0)
> Unpacking objects: 100% (20/20), done.
> From ../repo_2
>  * [new branch]      master     -> repo_jpeg2pdf/master
```
ブランチ一覧を表示
```
repo_1$ git branch -a
> * master
>   remotes/remote_repo_2/master
repo_1$ git remote -v
> remote_repo_2	../repo_2 (fetch)
> remote_repo_2	../repo_2 (push)
```
単純にマージするとエラーが出る
```
repo_1$ git merge remote_repo_2/master
> fatal: refusing to merge unrelated histories
```
強制的にマージを行う
```
repo_1$ git merge --allow-unrelated-histories remote_repo_2/master
> Merge made by the 'recursive' strategy.
>  file_2.txt | 324 +++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++
>  1 file changed, 324 insertions(+)
>  create mode 100644 file_2.txt
```
不要となった（リモートからコピーするために使った）ブランチを削除する
```
repo_1$ git branch --no-merged
>    ← マージされていないブランチは存在しない（この行には何も表示されない）
repo_1$ git branch -r -d remote_repo_2/master
```
リモート接続設定も削除する
```
repo_1$ git remote remove remote_repo_2
```


---
## 別のリポジトリを、指定のディレクトリ下に結合する

ローカルディスク内に、既存のリポジトリ repo_1 と、新しく作成するリポジトリ repo_all があるとする。
```
[dir]
  |
  +-- [repo_1]
  |     +- file_1.txt
  |
  +-- [repo_all]  … 新規作成
```
この repo_1 を repo_all 内のサブディレクトリに取り込む。取り込み後のは次のようになる。
```
[dir]
  |
  +-- [repo_1]
  |     +- file_1.txt
  |
  +-- [repo_all]
        +- dir_1
             +- file_1.txt
```
```git subtree``` または ```git read-tree``` コマンドを用いてディレクトリを新規作成しつつ取り込むことが出来る。

ただし、 ファイルごとの履歴は ```git log --follow [filename]``` と明示的に追跡を指定（--follow）しても追跡することは出来ない。

---
作業手順

repo_all を新規作成し、repo_1をリモートで接続する

```
$ mkdir repo_all

$ cd repo_all

repo_all$ git init
> Initialized empty Git repository in /home/user/repo_all/.git/

repo_all$ git remote add rem_repo_1 ../repo_1/

repo_all$ git remote -v
> rem_repo_1	../repo_1/ (fetch)
> rem_repo_1	../repo_1/ (push)
```

リモートの内容を取り込む
```
repo_all$ git fetch --all
> Fetching rem_repo_1
> remote: Counting objects: 6, done.
> remote: Compressing objects: 100% (2/2), done.
> remote: Total 6 (delta 0), reused 0 (delta 0)
> Unpacking objects: 100% (6/6), done.
> From ../repo_1
> * [new branch]      master     -> rem_repo_1/master
```

この時点では、まだファイルは同期されていない（ディレクトリ内にファイルは何もない）
```
repo_all$ ls -a
> ./  ../  .git/
```

```git subtree```コマンドで、内部ディレクトリ ```dir_1``` にリモートの内容を取り込む命令を出すが、この時点ではエラーとなる
```
repo_all$ git subtree add --prefix=dir_1 rem_repo_1 main
> fatal: ambiguous argument 'HEAD': unknown revision or path not in the working tree.
> Use '--' to separate paths from revisions, like this:
> 'git <command> [<revision>...] -- [<file>...]'
>Working tree has modifications.  Cannot add.
```

エラーの原因は、repo_allリポジトリに何も入っていない、1回もコミットが行われていないから。空のコミット（```git commit --allow-empty -m "msg"```）でもよいが、今回は適当なファイル（```.gitignore```）を作成しコミットする
```
repo_all$ echo -e "*.bak\n" > .gitignore 

repo_all$ git add .gitignore

repo_all$ git commit -m "add .gitignore, first commit"
> [master (root-commit) 51beb67] add .gitignore, first commit for exec subtree
> 1 file changed, 2 insertions(+)
> create mode 100644 .gitignore
```

その後、```git subtree```コマンドを実行すると成功する
```
repo_all$ git subtree add --prefix=dir_1 rem_repo_1 main
> git fetch rem_repo_1 main
> From ../repo_1
> * branch            main       -> FETCH_HEAD
>Added dir 'dir_1'
```

```git --subtree```ではなく、次のように```git read-tree```コマンドを用いることもできる
```
repo_all$ git read-tree --prefix=dir_1 -u rem_repo_1/main
```

ディレクトリ内に、リモートのファイルが反映される
```
repo_all$ ls -a
> ./  ../  .git/  .gitignore  dir_1/

repo_all$ ls -a dir_1/
> ./  ../  file_1.txt
```

---
## ファイルの履歴を追跡できる形で、別のリポジトリを指定ディレクトリ以下にコピーする

```git log```によるファイル追跡は、ファイルを別ディレクトリに移動・改名（```git mv```）すれば、```git log --follow```と明示的に追跡を指定しても、追跡することは出来ない。

今回取り上げるのは、ファイルを別ディレクトリに移動（```git mv file dir/```相当の処理）を行っても、追跡できるようにするトリックである。

```
[dir]
  |
  +-- [repo_1]
  |     +- file01.txt
  |
  +-- [repo_all]  … 既存のリポジトリ
```
この repo_1 を repo_all 内のサブディレクトリに取り込む。取り込み後のは次のようになる。
```
[dir]
  |
  +-- [repo_1]
  |     +- file_1.txt
  |
  +-- [repo_all]
        +- dir_1  … 新ディレクトリ内に repo_1の内容をコピー
             +- file_1.txt
```

---
***作業手順 第1段階（作業用リポジトリにコピーする）***

まず、repo_1の破壊を避けるため、作業用のリポジトリrepo_tempを新たに作成し、そこでdir_1内に全てのファイルを移動する。移動後は次のような形になる
```
[dir]
  |
  +-- [repo_1]
  |     +- file_1.txt
  |
  +-- [repo_temp]  … 作業用に一時リポジトリを作成する
        +- dir_1  … 新ディレクトリ内に repo_1の内容をコピー
             +- file_1.txt
```

repo_tempを作成
```
$ mkdir repo_temp && cd $_

repo_temp$ git init
> Initialized empty Git repository in /home/user/repo_temp/.git/
```
リモートリポジトリを関連付け、ファイルを取り込む
```
repo_temp$ git remote add rem_repo_1 ../repo_1/

repo_temp$ git fetch --all
> Fetching rem_repo_1
> remote: Counting objects: 9, done.
> remote: Compressing objects: 100% (5/5), done.
> remote: Total 9 (delta 0), reused 0 (delta 0)
> Unpacking objects: 100% (9/9), done.
> From ../repo_1
> * [new branch]      main       -> rem_repo_1/main

repo_temp$ git merge --allow-unrelated-histories rem_repo_1/main
```
リモートの関連付けを削除する
```
repo_temp$ git remote remove rem_repo_1 
```
この時点での状態を確認
```
repo_temp$  git ls-files -s
> .gitignore
> file01.txt

repo_temp$ git branch -a
> * master

repo_temp$ git remote -v
```
ファイルの履歴も、ディレクトリ移動前なので当然ながら追跡できる
```
repo_temp$ git log --oneline
> 1f64e94 (HEAD -> master) 2nd edit file01.txt (repo_1)
> 2849c01 new file01.txt (repo_1)
> 4cd1f1a first commit (repo_1)

repo_temp$ git log --oneline file01.txt
> 1f64e94 (HEAD -> master) 2nd edit file01.txt (repo_1)
> 2849c01 new file01.txt (repo_1)
```

---
***作業手順 第2段階（作業用リポジトリ内でファイルをディレクトリに移動）***

```git filter-branch --index-filter```を使って、履歴追跡を維持したまま、ファイルを全て新ディレクトリ（dir_1）に移動する。

```
repo_temp$ git filter-branch --index-filter 'git ls-files -s | sed "s@\t\"*@&dir_1/@" | \
  GIT_INDEX_FILE=$GIT_INDEX_FILE.new git update-index --index-info &&
  mv $GIT_INDEX_FILE.new $GIT_INDEX_FILE || true' HEAD

> Rewrite 4cd1f1ab137d7fa8fc45aa9eea38f998b817487e (1/3) (0 seconds passed, remaining 0 predicted)    mv: '/home/user/repo_temp/.git-rewrite/t/../index.new' を stat できません: そのようなファイルやディレクトリはありません
> Rewrite 2849c014ebe065837046a170f92270a770e46f99 (2/3) (0 seconds passed, remainRewrite 1f64e94f8ea1067b54c145aa6b227ee7ff16b484 (3/3) (0 seconds passed, remaining 0 predicted)    
> Ref 'refs/heads/master' was rewritten
```
ファイルの状況と、ログが追跡できているか確認する
```
repo_temp$ git ls-files
> dir_1/.gitignore
> dir_1/file01.txt

repo_temp$ git log --oneline
> f409b56 (HEAD -> master) 2nd edit file01.txt (repo_1)
> 6514ca3 new file01.txt (repo_1)
> 4cd1f1a first commit (repo_1)

repo_temp$ git log --oneline dir_1/file01.txt
> f409b56 (HEAD -> master) 2nd edit file01.txt (repo_1)
> 6514ca3 new file01.txt (repo_1)
```

---
***作業手順 第3段階（作業用リポジトリから目的リポジトリにコピー）***

第2段階で作成したリポジトリを、既存のrepo_allリポジトリに取り込む。最初に、repo_allの状態を表示してみる。.gitignore というファイルが1つある状態だ

```
repo_temp$ cd ../repo_all/

repo_all$ git ls-files
> .gitignore

/repo_all$ git log --oneline
> 95e44eb (HEAD -> master) first commit, add .gitignore (repo_all)
```

リモート リポジトリとして、第1〜第2段階で作成した作業用リポジトリを登録し、取り込む

```
repo_all$ git remote add rem_repo_temp ../repo_temp/

repo_all$ git fetch --all
> Fetching rem_repo_temp
> warning: no common commits
> remote: Counting objects: 11, done.
> remote: Compressing objects: 100% (5/5), done.
> remote: Total 11 (delta 0), reused 0 (delta 0)
> Unpacking objects: 100% (11/11), done.
> From ../repo_temp
>  * [new branch]      master     -> rem_repo_temp/master

repo_all$ git merge --allow-unrelated-histories rem_repo_temp/master 
> Merge made by the 'recursive' strategy.
>  repo_1/.gitignore | 2 ++
>  repo_1/file01.txt | 3 +++
>  2 files changed, 5 insertions(+)
>  create mode 100644 repo_1/.gitignore
>  create mode 100644 repo_1/file01.txt
```

リモート接続を解除する
```
repo_all$ git remote remove rem_repo_temp
```

作業用リポジトリからファイルが取り込まれた状況を確認する
```
repo_all$ git branch -a
> * master

repo_all$ git ls-files
> .gitignore
> dir_1/.gitignore
> dir_1/file01.txt

repo_all$ git log --oneline --graph
> *   2bbf2f2 (HEAD -> master) Merge remote-tracking branch 'rem_repo_temp/master'
> |\  
> | * f409b56 2nd edit file01.txt (repo_1)
> | * 6514ca3 new file01.txt (repo_1)
> | * 4cd1f1a first commit (repo_1)
> * 95e44eb first commit, add .gitignore (repo_all)
```

取り込んだリポジトリのファイル履歴が追跡可能なのか確認する
```
repo_all$ git log --oneline dir_1/file01.txt
> f409b56 2nd edit file01.txt (repo_1)
> 6514ca3 new file01.txt (repo_1)
```

ファイル履歴が追跡可能な状態で、ディレクトリ内に外部リポジトリをまるごとコピーしてくることが出来た。

***（参考）不要なブランチが作成された場合の削除方法***

```git log --oneline```でリポジトリ全体のログを表示したとき、```HEAD -> master```以外に```refs/original/refs/heads/master```という不要なブランチが表示されることがある。

このブランチをログを残さずきれいに削除するには
```
$ git update-ref -d refs/original/refs/heads/master
```
