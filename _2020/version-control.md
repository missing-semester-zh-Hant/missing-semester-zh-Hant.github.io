---
layout: lecture
title: "版本控制 (Git) （尚未翻譯）"
date: 2019-01-22
ready: true
video:
  aspect: 56.25
  id: 2sjqTHE0zok
---

<!-- Version control systems (VCSs) are tools used to track changes to source code
(or other collections of files and folders). As the name implies, these tools
help maintain a history of changes; furthermore, they facilitate collaboration.
VCSs track changes to a folder and its contents in a series of snapshots, where
each snapshot encapsulates the entire state of files/folders within a top-level
directory. VCSs also maintain metadata like who created each snapshot, messages
associated with each snapshot, and so on. -->
版本控制系統 (VCSs) 是用來追蹤程式原始碼（或者檔案與檔案夾）內在改動的工具。
如同名字一樣，這些工具將會幫助我們管理程式碼的修改歷史記錄；更進一步，它也促進了合作。
VCS透過一系列快照將檔案夾與其內容保存起來，每個快照都包含了檔案夾或者檔案的完整訊息。
同時它還維護類似於快照創建者以及每個快照的相關信息等元數據。

<!-- Why is version control useful? Even when you're working by yourself, it can let
you look at old snapshots of a project, keep a log of why certain changes were
made, work on parallel branches of development, and much more. When working
with others, it's an invaluable tool for seeing what other people have changed,
as well as resolving conflicts in concurrent development. -->
爲什麼版本控制系統十分有用？即使我們一個人進行工作，它也可以提供讓我們閱讀專案舊快照的能力，
記錄每次編輯的目的，以及基於多個分支並行開發。
與別人協同開發時，它展現了檢視別人對程式碼修改的能力，同時可以解決憂鬱並行開發引起的衝突。

<!-- Modern VCSs also let you easily (and often automatically) answer questions
like: -->
現代的版本控制系統可以幫助我們輕鬆（經常是全自動）地回答以下問題：

<!-- - Who wrote this module?
- When was this particular line of this particular file edited? By whom? Why
  was it edited?
- Over the last 1000 revisions, when/why did a particular unit test stop
working? -->
- 誰建立了這個模塊？
- 檔案的這一部分是什麼時候被編輯的？是誰做出的編輯？目的是什麼？
- 最近的 1000 個版本中，什麼時候/什麼原因導致單元測試失敗了？

<!-- While other VCSs exist, **Git** is the de facto standard for version control.
This [XKCD 漫畫](https://xkcd.tw/1597) captures Git's reputation: -->
版本控制系統有很多，但是 **Git** 是所有版本控制系統的標準。
這篇 [XKCD 漫畫](https://xkcd.tw/1597) 展現了Git的聲望：

![xkcd 1597](https://xkcd.tw/strip/1597.jpg)

<!-- Because Git's interface is a leaky abstraction, learning Git top-down (starting
with its interface / command-line interface) can lead to a lot of confusion.
It's possible to memorize a handful of commands and think of them as magic
incantations, and follow the approach in the comic above whenever anything goes
wrong. -->
因爲 Git 的抽象泄漏（leaky abstraction）問題，從總體到細節的方式（從介面開始）的學習方式會令人感到非常困惑。
很多時候我們只能記住一些指令，然後如同詠唱魔法一般地使用他們。一旦出現問題，就只能如同上篇的漫畫一樣處理了。

<!-- While Git admittedly has an ugly interface, its underlying design and ideas are
beautiful. While an ugly interface has to be _memorized_, a beautiful design
can be _understood_. For this reason, we give a bottom-up explanation of Git,
starting with its data model and later covering the command-line interface.
Once the data model is understood, the commands can be better understood, in
terms of how they manipulate the underlying data model. -->
Git 的介面非常醜陋，但他有優雅的底層設計與思考方式。
醜陋的介面只能被背誦，而優雅的底層會非常容易被理解。
所以，我們將從底層到介面的方式介紹Git。
從資料模型開始，最後再學習介面。
一旦我們理解了Git的資料模型，學習介面並理解介面是如何操作底層資料將會非常容易。

<!-- # Git's data model -->
# Git 的資料模型

<!-- There are many ad-hoc approaches you could take to version control. Git has a
well thought-out model that enables all the nice features of version control,
like maintaining history, supporting branches, and enabling collaboration. -->.
有許多方式可以做到版本控制。
Git 使用良好設計的模型，來支援版本控制所需要的所有功能，例如維護歷史記錄，記錄分支與合作支援。

<!-- ## Snapshots -->
## 快照

<!-- Git models the history of a collection of files and folders within some
top-level directory as a series of snapshots. In Git terminology, a file is
called a "blob", and it's just a bunch of bytes. A directory is called a
"tree", and it maps names to blobs or trees (so directories can contain other
directories). A snapshot is the top-level tree that is being tracked. For
example, we might have a tree as follows: -->
Git 將頂級目錄中檔案與檔案夾的集合的歷史記錄建立爲一系列快照。
在 Git 內，檔案被稱爲「blob」，只是一堆字元。目錄被成爲「樹」，並且它將名稱映射到Blob或樹（因此目錄可以包含其他目錄）。快照是被追蹤的頂級樹。例如，我們可能有如下的一棵樹：

```
<root> (tree)
|
+- foo (tree)
|  |
|  + bar.txt (blob, contents = "hello world")
|
+- baz.txt (blob, contents = "git is wonderful")
```

<!-- The top-level tree contains two elements, a tree "foo" (that itself contains
one element, a blob "bar.txt"), and a blob "baz.txt". -->
頂級樹包含兩個元素，一棵樹「foo」（本身包含一個元素，即blob 「bar.txt」），和一個blob 「baz.txt」。

<!-- ## Modeling history: relating snapshots -->
## 建模歷史：相關快照

<!-- How should a version control system relate snapshots? One simple model would be
to have a linear history. A history would be a list of snapshots in time-order.
For many reasons, Git doesn't use a simple model like this. -->
版本控制系統應該如何關聯快照？一種簡單的模型是建立線性歷史記錄。歷史記錄將是按照時間順序排列的快照列表。
出於許多原因，Git沒有使用這樣的簡單模型。

<!-- In Git, a history is a directed acyclic graph (DAG) of snapshots. That may
sound like a fancy math word, but don't be intimidated. All this means is that
each snapshot in Git refers to a set of "parents", the snapshots that preceded
it. It's a set of parents rather than a single parent (as would be the case in
a linear history) because a snapshot might descend from multiple parents, for
example due to combining (merging) two parallel branches of development. -->
在 Git 中，歷史記錄是快照的有向無環圖 (DAG)。這聽起來像個花哨的數學詞，不要被嚇到。
這意味着 Git 中的每一個快照都參考一組「母對象」，即之前的快照。
他是一組母節點集合而非單個母節點，因爲他可能從多個母節點中繼承，例如合併後的兩條分支。

<!-- Git calls these snapshots "commit"s. Visualizing a commit history might look
something like this: -->
Git 將這些快照稱作 「提交」(commit)。將其可視化後大概像這樣：

```
o <-- o <-- o <-- o
            ^  
             \
              --- o <-- o
```

<!-- In the ASCII art above, the `o`s correspond to individual commits (snapshots).
The arrows point to the parent of each commit (it's a "comes before" relation,
not "comes after"). After the third commit, the history branches into two
separate branches. This might correspond to, for example, two separate features
being developed in parallel, independently from each other. In the future,
these branches may be merged to create a new snapshot that incorporates both of
the features, producing a new history that looks like this, with the newly
created merge commit shown in bold: -->
在上圖中，其中的 `o` 代表一次提交。
箭頭指出的是當前的母節點。（注意是「在自己之前」的節點，而非「之後」）。
在第三次提交之後，歷史記錄將分為兩個單獨的分支。 
例如，這可能對應於兩個單獨的功能，他們彼此獨立，並行開發。
將來，這些分支可能會合併以建立一個融合了這兩個功能的新快照，從而生成看起來像下圖這樣的新歷史記錄，新創建的合併提交以粗體顯示：

<pre>
o <-- o <-- o <-- o <---- <strong>o</strong>
            ^            /
             \          v
              --- o <-- o
</pre>

<!-- Commits in Git are immutable. This doesn't mean that mistakes can't be
corrected, however; it's just that "edits" to the commit history are actually
creating entirely new commits, and references (see below) are updated to point
to the new ones. -->
Git中的提交是不可更改的。 
但這並不意味著不能糾正錯誤。 
只是對提交歷史記錄的“編輯”實際上是建立全新的提交，並且參考（參見下文）也已更新為指向新的提交。

<!-- ## Data model, as pseudocode -->
## 以假碼表示的資料模型

<!-- It may be instructive to see Git's data model written down in pseudocode: -->
假碼記錄下來的Git數據模型可能更易於理解：

```
// a file is a bunch of bytes
type blob = array<byte>

// a directory contains named files and directories
type tree = map<string, tree | blob>

// a commit has parents, metadata, and the top-level tree
type commit = struct {
    parent: array<commit>
    author: string
    message: string
    snapshot: tree
}
```

<!-- It's a clean, simple model of history. -->
這是一個簡單整潔的歷史模型。

<!-- ## Objects and content-addressing -->
## 對象與內容定位

<!-- An "object" is a blob, tree, or commit: -->
一個「對象」是一個blob，樹或者提交：

```
type object = blob | tree | commit
```

<!-- In Git data store, all objects are content-addressed by their [SHA-1
hash](https://en.wikipedia.org/wiki/SHA-1). -->
Git在存儲資料時，所有對象都被記錄[SHA-1 hash](https://en.wikipedia.org/wiki/SHA-1)以供定位。

```
objects = map<string, object>

def store(object):
    id = sha1(object)
    objects[id] = object

def load(id):
    return objects[id]
```

<!-- Blobs, trees, and commits are unified in this way: they are all objects. When
they reference other objects, they don't actually _contain_ them in their
on-disk representation, but have a reference to them by their hash. -->
Blob，樹和提交以這種方式統一：它們都是對象。 
當他們參考其他對象時，它們實際上並沒有真正被 _寫入_ 硬碟，而是通過哈希值對其進行引用。

<!-- For example, the tree for the example directory structure [above](#snapshots)
(visualized using `git cat-file -p 698281bc680d1995c5f4caaf3359721a5a58d48d`),
looks like this: -->
例如，示例目錄[above](#snapshots)中的樹（使用 `git cat-file -p 698281bc680d1995c5f4caaf3359721a5a58d48d` 來顯示）看起來像這樣：

```
100644 blob 4448adbf7ecd394f42ae135bbeed9676e894af85    baz.txt
040000 tree c68d233a33c5c06e0340e4c224f0afca87c8ce87    foo
```

<!-- The tree itself contains pointers to its contents, `baz.txt` (a blob) and `foo`
(a tree). If we look at the contents addressed by the hash corresponding to
baz.txt with `git cat-file -p 4448adbf7ecd394f42ae135bbeed9676e894af85`, we get
the following: -->
樹本身會包含指向其他內容的指標，例如 `baz.txt` （一個 blob ）與 `foo` （一棵樹）。
如果我們使用 `git cat-file -p 4448adbf7ecd394f42ae135bbeed9676e894af85` 來透過hash查看 `baz.txt` 的內容，
我們會得到這種結果：

```
git is wonderful
```

<!-- ## References -->
## 參考

<!-- Now, all snapshots can be identified by their SHA-1 hash. That's inconvenient,
because humans aren't good at remembering strings of 40 hexadecimal characters. -->
現在，所有的快照都通過其SHA-1值識別，這對於人類來說太不方便了，我們不善於記憶長達40個16進位的字串。

<!-- Git's solution to this problem is human-readable names for SHA-1 hashes, called
"references". References are pointers to commits. Unlike objects, which are
immutable, references are mutable (can be updated to point to a new commit).
For example, the `master` reference usually points to the latest commit in the
main branch of development. -->
Git解決此問題的方法是爲SHA-1值提供易於理解的名稱，稱爲"參考"。
參考是指向提交的指標。
與不可變的對象不同，參考是可變的（可以更新以指向新的提交）。
例如，`master` 通常指向主分支的最新提交。

```
references = map<string, string>

def update_reference(name, id):
    references[name] = id

def read_reference(name):
    return references[name]

def load_reference(name_or_id):
    if name_or_id in references:
        return load(references[name_or_id])
    else:
        return load(name_or_id)
```

<!-- With this, Git can use human-readable names like "master" to refer to a
particular snapshot in the history, instead of a long hexadecimal string. -->
這樣，Git可以使用人類可以理解的名稱，例如 "master" 來參考歷史記錄中的特定快照，而非使用16進位字串。

<!-- One detail is that we often want a notion of "where we currently are" in the
history, so that when we take a new snapshot, we know what it is relative to
(how we set the `parents` field of the commit). In Git, that "where we
currently are" is a special reference called "HEAD". -->

我們需要注意一個細節，就是我們常常會需要知道在全部歷史記錄中 "我們目前的位置"，
以便在建立新快照時，我們知道快照的相對於誰（提交中的 `parents`， 即母節點）。
"我們目前的位置" 是一個特別的參考，稱作 "HEAD"。

<!-- ## Repositories -->
## 倉儲

<!-- Finally, we can define what (roughly) is a Git _repository_: it is the data
`objects` and `references`. -->
終於，我們可以大致定義Git _倉儲_ 是什麼了：是資料物件 `objects` 與其參考 `references`。

<!-- On disk, all Git stores are objects and references: that's all there is to Git's
data model. All `git` commands map to some manipulation of the commit DAG by
adding objects and adding/updating references. -->
在硬碟上，所有 Git 存儲都是物件與參考。
所有的 `git` 指令都對應某種建立對象，增添或刪除參考的操作。

<!-- Whenever you're typing in any command, think about what manipulation the
command is making to the underlying graph data structure. Conversely, if you're
trying to make a particular kind of change to the commit DAG, e.g. "discard
uncommitted changes and make the 'master' ref point to commit `5d83f9e`", there's
probably a command to do it (e.g. in this case, `git checkout master; git reset
--hard 5d83f9e`). -->
當我們輸入指令時，想一想這個指令對基礎的圖數據結構進行了什麼操作。
如果要對提交DAG進行特定類型的編輯，例如 "扔掉未提交的編輯與更改 'master' 參考至 `5d83f9e`"時，
有什麼指令可以執行此編輯。
（此例中，我們可以使用 `git checkout master; git reset --hard 5d83f9e`）

<!-- # Staging area -->
# 暫存區域 （"staging area"）

<!-- This is another concept that's orthogonal to the data model, but it's a part of
the interface to create commits. -->
這是一個與資料模型無關的概念，但他是建立提交的介面的一部分。

<!-- One way you might imagine implementing snapshotting as described above is to have
a "create snapshot" command that creates a new snapshot based on the _current
state_ of the working directory. Some version control tools work like this, but
not Git. We want clean snapshots, and it might not always be ideal to make a
snapshot from the current state. For example, imagine a scenario where you've
implemented two separate features, and you want to create two separate commits,
where the first introduces the first feature, and the next introduces the
second feature. Or imagine a scenario where you have debugging print statements
added all over your code, along with a bugfix; you want to commit the bugfix
while discarding all the print statements. -->
在我們之前介紹的快照機制中，也許你想實現一個 "建立快照" 的指令，此指令可以基於工作目錄的 _目前狀態_ 建立新的快照。
有些版本控制系統的工作方式與此類似，但是 Git 不是。
我們希望快照簡潔乾淨，從當前狀態建立快照可能不是個好選擇。
例如，考慮這個場景：
我們實現了兩個單獨的功能，並且想要建立兩個獨立的提交，體一個提交僅含有第一個功能，第二個提交了另一個功能。
或者，設想另一種場景：
我們向代碼中添加了許多打印語句以及錯誤修正，而我們希望放棄所有打印語句的同時提交錯誤修正。

<!-- Git accommodates such scenarios by allowing you to specify which modifications
should be included in the next snapshot through a mechanism called the "staging
area". -->
Git透過 "暫存區域" 來允許我們指定下次快照中要包含哪些改動。

<!-- # Git command-line interface -->
# Git 的命令列介面

<!-- To avoid duplicating information, we're not going to explain the commands below
in detail. See the highly recommended [Pro Git](https://git-scm.com/book/en/v2)
for more information, or watch the lecture video. -->
爲了避免信息重複，我們不會詳細解釋。
十分建議閱讀 [Pro Git](https://git-scm.com/book/en/v2) 或者觀看課程回放來學習。

<!-- ## Basics -->
## 基礎

{% comment %}

The `git init` command initializes a new Git repository, with repository
metadata being stored in the `.git` directory:

```console
$ mkdir myproject
$ cd myproject
$ git init
Initialized empty Git repository in /home/missing-semester/myproject/.git/
$ git status
On branch master

No commits yet

nothing to commit (create/copy files and use "git add" to track)
```

How do we interpret this output? "No commits yet" basically means our version
history is empty. Let's fix that.

```console
$ echo "hello, git" > hello.txt
$ git add hello.txt
$ git status
On branch master

No commits yet

Changes to be committed:
  (use "git rm --cached <file>..." to unstage)

        new file:   hello.txt

$ git commit -m 'Initial commit'
[master (root-commit) 4515d17] Initial commit
 1 file changed, 1 insertion(+)
 create mode 100644 hello.txt
```

With this, we've `git add`ed a file to the staging area, and then `git
commit`ed that change, adding a simple commit message "Initial commit". If we
didn't specify a `-m` option, Git would open our text editor to allow us type a
commit message.

Now that we have a non-empty version history, we can visualize the history.
Visualizing the history as a DAG can be especially helpful in understanding the
current status of the repo and connecting it with your understanding of the Git
data model.

The `git log` command visualizes history. By default, it shows a flattened
version, which hides the graph structure. If you use a command like `git log
--all --graph --decorate`, it will show you the full version history of the
repository, visualized in graph form.

```console
$ git log --all --graph --decorate
* commit 4515d17a167bdef0a91ee7d50d75b12c9c2652aa (HEAD -> master)
  Author: Missing Semester <missing-semester@mit.edu>
  Date:   Tue Jan 21 22:18:36 2020 -0500

      Initial commit
```

This doesn't look all that graph-like, because it only contains a single node.
Let's make some more changes, author a new commit, and visualize the history
once more.

```console
$ echo "another line" >> hello.txt
$ git status
On branch master
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

        modified:   hello.txt

no changes added to commit (use "git add" and/or "git commit -a")
$ git add hello.txt
$ git status
On branch master
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

        modified:   hello.txt

$ git commit -m 'Add a line'
[master 35f60a8] Add a line
 1 file changed, 1 insertion(+)
```

Now, if we visualize the history again, we'll see some of the graph structure:

```
* commit 35f60a825be0106036dd2fbc7657598eb7b04c67 (HEAD -> master)
| Author: Missing Semester <missing-semester@mit.edu>
| Date:   Tue Jan 21 22:26:20 2020 -0500
|
|     Add a line
|
* commit 4515d17a167bdef0a91ee7d50d75b12c9c2652aa
  Author: Anish Athalye <me@anishathalye.com>
  Date:   Tue Jan 21 22:18:36 2020 -0500

      Initial commit
```

Also, note that it shows the current HEAD, along with the current branch
(master).

We can look at old versions using the `git checkout` command.

```console
$ git checkout 4515d17  # previous commit hash; yours will be different
Note: checking out '4515d17'.

You are in 'detached HEAD' state. You can look around, make experimental
changes and commit them, and you can discard any commits you make in this
state without impacting any branches by performing another checkout.

If you want to create a new branch to retain commits you create, you may
do so (now or later) by using -b with the checkout command again. Example:

  git checkout -b <new-branch-name>

HEAD is now at 4515d17 Initial commit
$ cat hello.txt
hello, git
$ git checkout master
Previous HEAD position was 4515d17 Initial commit
Switched to branch 'master'
$ cat hello.txt
hello, git
another line
```

Git can show you how files have evolved (differences, or diffs) using the `git
diff` command:

```console
$ git diff 4515d17 hello.txt
diff --git c/hello.txt w/hello.txt
index 94bab17..f0013b2 100644
--- c/hello.txt
+++ w/hello.txt
@@ -1 +1,2 @@
 hello, git
 +another line
```

{% endcomment %}

<!-- - `git help <command>`: get help for a git command
- `git init`: creates a new git repo, with data stored in the `.git` directory
- `git status`: tells you what's going on
- `git add <filename>`: adds files to staging area
- `git commit`: creates a new commit
    - Write [good commit messages](https://tbaggery.com/2008/04/19/a-note-about-git-commit-messages.html)!
    - Even more reasons to write [good commit messages](https://chris.beams.io/posts/git-commit/)!
- `git log`: shows a flattened log of history
- `git log --all --graph --decorate`: visualizes history as a DAG
- `git diff <filename>`: show differences since the last commit
- `git diff <revision> <filename>`: shows differences in a file between snapshots
- `git checkout <revision>`: updates HEAD and current branch -->
- `git help <command>`: 獲取幫助
- `git init`: 建立新的倉儲，相關資料會寫入 `.git` 檔案夾
- `git status`: 顯示當前倉儲狀態
- `git add <filename>`: 添加到暫存區域
- `git commit`: 建立新提交
    - 寫入 [優秀的提交信息](https://tbaggery.com/2008/04/19/a-note-about-git-commit-messages.html)!
    - 透過更多方式寫入 [優秀的提交信息](https://chris.beams.io/posts/git-commit/)!
- `git log`: 以詳細信息顯示歷史日誌
- `git log --all --graph --decorate`: 以 DAG 方式顯示歷史日誌
- `git diff <filename>`: 展示與上一次提交時的差異
- `git diff <revision> <filename>`: 展示某檔案與上一次提交時的差異
- `git checkout <revision>`: 更新 HEAD 與當前分支


<!-- ## Branching and merging -->
## 分支與合併

{% comment %}

Branching allows you to "fork" version history. It can be helpful for working
on independent features or bug fixes in parallel. The `git branch` command can
be used to create new branches; `git checkout -b <branch name>` creates and
branch and checks it out.

Merging is the opposite of branching: it allows you to combine forked version
histories, e.g. merging a feature branch back into master. The `git merge`
command is used for merging.

{% endcomment %}

<!-- - `git branch`: shows branches
- `git branch <name>`: creates a branch
- `git checkout -b <name>`: creates a branch and switches to it
    - same as `git branch <name>; git checkout <name>`
- `git merge <revision>`: merges into current branch
- `git mergetool`: use a fancy tool to help resolve merge conflicts
- `git rebase`: rebase set of patches onto a new base -->
- `git branch`: 顯示分支
- `git branch <name>`: 建立分支
- `git checkout -b <name>`: 建立分支並切換至該分支
    - 等同於 `git branch <name>; git checkout <name>`
- `git merge <revision>`: 合併到當前分支
- `git mergetool`: 使用神奇工具處理合併衝突
- `git rebase`: rebase set of patches onto a new base

<!-- ## Remotes -->
## 遠端

<!-- - `git remote`: list remotes
- `git remote add <name> <url>`: add a remote
- `git push <remote> <local branch>:<remote branch>`: send objects to remote, and update remote reference
- `git branch --set-upstream-to=<remote>/<remote branch>`: set up correspondence between local and remote branch
- `git fetch`: retrieve objects/references from a remote
- `git pull`: same as `git fetch; git merge`
- `git clone`: download repository from remote -->
- `git remote`: 列出遠端
- `git remote add <name> <url>`: 添加遠端
- `git push <remote> <local branch>:<remote branch>`: 將物件推送至遠端，並且更新遠端參考
- `git branch --set-upstream-to=<remote>/<remote branch>`: 建立本地分支與遠端分支的關聯
- `git fetch`: 從遠端擷取物件/參考
- `git pull`: 等同於 `git fetch; git merge`
- `git clone`: 從遠端下載倉儲

<!-- ## Undo -->
## 回滾

- `git commit --amend`: 編輯提交的內容或信息
- `git reset HEAD <file>`: 取消暫存檔案
- `git checkout -- <file>`: 回滾更改

<!-- # Advanced Git -->
# 高級技巧

<!-- - `git config`: Git is [highly customizable](https://git-scm.com/docs/git-config)
- `git clone --depth=1`: shallow clone, without entire version history
- `git add -p`: interactive staging
- `git rebase -i`: interactive rebasing
- `git blame`: show who last edited which line
- `git stash`: temporarily remove modifications to working directory
- `git bisect`: binary search history (e.g. for regressions)
- `.gitignore`: [specify](https://git-scm.com/docs/gitignore) intentionally untracked files to ignore -->
- `git config`: Git 是 [高度可自訂的](https://git-scm.com/docs/git-config)
- `git clone --depth=1`: 下載倉儲，但不下載歷史記錄
- `git add -p`: 交互暫存
- `git rebase -i`: 交互rebasing
- `git blame`: 查看最後修改某列的使用者
- `git stash`: 暫時移除工作目錄下的更改
- `git bisect`: 透過二分搜尋來搜尋歷史記錄（比如回歸）
- `.gitignore`: [指定](https://git-scm.com/docs/gitignore) 忽視且不會再追蹤的檔案

<!-- # Miscellaneous -->
# 雜項

<!-- - **GUIs**: there are many [GUI clients](https://git-scm.com/downloads/guis)
out there for Git. We personally don't use them and use the command-line
interface instead.
- **Shell integration**: it's super handy to have a Git status as part of your
shell prompt ([zsh](https://github.com/olivierverdier/zsh-git-prompt),
[bash](https://github.com/magicmonty/bash-git-prompt)). Often included in
frameworks like [Oh My Zsh](https://github.com/ohmyzsh/ohmyzsh).
- **Editor integration**: similarly to the above, handy integrations with many
features. [fugitive.vim](https://github.com/tpope/vim-fugitive) is the standard
one for Vim.
- **Workflows**: we taught you the data model, plus some basic commands; we
didn't tell you what practices to follow when working on big projects (and
there are [many](https://nvie.com/posts/a-successful-git-branching-model/)
[different](https://www.endoflineblog.com/gitflow-considered-harmful)
[approaches](https://www.atlassian.com/git/tutorials/comparing-workflows/gitflow-workflow)).
- **GitHub**: Git is not GitHub. GitHub has a specific way of contributing code
to other projects, called [pull
requests](https://help.github.com/en/github/collaborating-with-issues-and-pull-requests/about-pull-requests).
- **Other Git providers**: GitHub is not special: there are many Git repository
hosts, like [GitLab](https://about.gitlab.com/) and
[BitBucket](https://bitbucket.org/). -->
- **圖形介面**: Git 有許多 [圖形介面](https://git-scm.com/downloads/guis)
不過我們使用命令列。
- **Shell 集成**: 將 Git 集成/一體化到 shell 中會非常合適。
([zsh](https://github.com/olivierverdier/zsh-git-prompt),
[bash](https://github.com/magicmonty/bash-git-prompt)). [Oh My Zsh](https://github.com/ohmyzsh/ohmyzsh) 這類框架中經常已經含有 Git 。
- **編輯器集成**: 與上相似，將 Git 集成至編輯器中也很合適。 [fugitive.vim](https://github.com/tpope/vim-fugitive) 是一個基本的Vim插件。
- **工作流**: 我們已經講解了資料模型與一些常見指令，但是還沒有討論在大型專案內工作時一些慣例。
(並且這裏有 [非常多](https://nvie.com/posts/a-successful-git-branching-model/)
[不同的](https://www.endoflineblog.com/gitflow-considered-harmful)
[處理方式](https://www.atlassian.com/git/tutorials/comparing-workflows/gitflow-workflow)).
- **GitHub**: Git 不是 GitHub. GitHub 需要使用 [pull
requests](https://help.github.com/en/github/collaborating-with-issues-and-pull-requests/about-pull-requests)來爲他人的專案貢獻程式碼。
- **其他 Git 提供者**: GitHub 不是唯一的，有許多類似於 [GitLab](https://about.gitlab.com/) 與
[BitBucket](https://bitbucket.org/)的倉儲商。


<!-- # Resources -->
# 資源

<!-- - [Pro Git](https://git-scm.com/book/en/v2) is **highly recommended reading**.
Going through Chapters 1--5 should teach you most of what you need to use Git
proficiently, now that you understand the data model. The later chapters have
some interesting, advanced material.
- [Oh Shit, Git!?!](https://ohshitgit.com/) is a short guide on how to recover
from some common Git mistakes.
- [Git for Computer
Scientists](https://eagain.net/articles/git-for-computer-scientists/) is a
short explanation of Git's data model, with less pseudocode and more fancy
diagrams than these lecture notes.
- [Git from the Bottom Up](https://jwiegley.github.io/git-from-the-bottom-up/)
is a detailed explanation of Git's implementation details beyond just the data
model, for the curious.
- [How to explain git in simple
words](https://smusamashah.github.io/blog/2017/10/14/explain-git-in-simple-words)
- [Learn Git Branching](https://learngitbranching.js.org/) is a browser-based
game that teaches you Git. -->
- [Pro Git](https://git-scm.com/book/en/v2) 是 **非常值得閱讀的**.
閱讀1-5章節可以教會你使流暢使用 Git 的大多數技巧，因爲你已經理解了 Git 的資料模型。
後面的章節提供了很多有取得高級主題。
- [Oh Shit, Git!?!](https://ohshitgit.com/) 是一個簡單的手冊來指引你如何從錯誤中恢復。
- [Git for Computer Scientists](https://eagain.net/articles/git-for-computer-scientists/) 
簡短介紹了 Git 的資料模型，與本文相比包含少量的假碼，但是增添了大量的精妙圖像。
- [Git from the Bottom Up](https://jwiegley.github.io/git-from-the-bottom-up/)
詳細介紹了 Git 的實現細節，不僅限於資料模型。適合好奇心強烈的同學。
- [How to explain git in simple
words](https://smusamashah.github.io/blog/2017/10/14/explain-git-in-simple-words)
- [Learn Git Branching](https://learngitbranching.js.org/) 透過小遊戲來學習 Git 。


# Exercises

1. If you don't have any past experience with Git, either try reading the first
   couple chapters of [Pro Git](https://git-scm.com/book/en/v2) or go through a
   tutorial like [Learn Git Branching](https://learngitbranching.js.org/). As
   you're working through it, relate Git commands to the data model.
1. Clone the [repository for the
class website](https://github.com/missing-semester/missing-semester).
    1. Explore the version history by visualizing it as a graph.
    1. Who was the last person to modify `README.md`? (Hint: use `git log` with
       an argument)
    1. What was the commit message associated with the last modification to the
       `collections:` line of `_config.yml`? (Hint: use `git blame` and `git
       show`)
1. One common mistake when learning Git is to commit large files that should
   not be managed by Git or adding sensitive information. Try adding a file to
   a repository, making some commits and then deleting that file from history
   (you may want to look at
   [this](https://help.github.com/articles/removing-sensitive-data-from-a-repository/)).
1. Clone some repository from GitHub, and modify one of its existing files.
   What happens when you do `git stash`? What do you see when running `git log
   --all --oneline`? Run `git stash pop` to undo what you did with `git stash`.
   In what scenario might this be useful?
1. Like many command line tools, Git provides a configuration file (or dotfile)
   called `~/.gitconfig`. Create an alias in `~/.gitconfig` so that when you
   run `git graph`, you get the output of `git log --all --graph --decorate
   --oneline`.
1. You can define global ignore patterns in `~/.gitignore_global` after running
   `git config --global core.excludesfile ~/.gitignore_global`. Do this, and
   set up your global gitignore file to ignore OS-specific or editor-specific
   temporary files, like `.DS_Store`.
1. Clone the [repository for the class
   website](https://github.com/missing-semester/missing-semester), find a typo
   or some other improvement you can make, and submit a pull request on GitHub.
