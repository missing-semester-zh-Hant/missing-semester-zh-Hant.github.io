---
layout: lecture
title: "元程式設計"
details: build systems, dependency management, testing, CI
date: 2019-01-27
ready: true
video:
  aspect: 56.25
  id: _Ms1Z4xfqv4
---

<!-- What do we mean by "metaprogramming"? Well, it was the best collective 
term we could come up with for the set of things that are more about
_process_ than they are about writing code or working more efficiently.
In this lecture, we will look at systems for building and testing your
code, and for managing dependencies. These may seem like they are of
limited importance in your day-to-day as a student, but the moment you
interact with a larger code base through an internship or once you enter
the "real world", you will see this everywhere. We should note that
"metaprogramming" can also mean "[programs that operate on
programs](https://en.wikipedia.org/wiki/Metaprogramming)", whereas that
is not quite the definition we are using for the purposes of this
lecture. -->

我們這裡說的元程式設計（metaprogramming）是什麼意思呢？好吧，對於本文要介紹的這些內容，這是我們能夠想到的最能概括它們的詞。因為我們今天要講的東西，更多是關於*流程* ，而不是寫程式碼或更高效的工作。本節課我們會學習構建系統、程式碼測試以及依賴管理。在您還是學生的時候，這些東西看上去似乎對您來說沒那麼重要，不過當您開始實習或出社會的時候，您將會接觸到大型的程式碼，本節課講授的這些東西也會變得隨處可見。必須要指出的是，元程式設計 也有[用於操作程式的程式](https://en.wikipedia.org/wiki/Metaprogramming)" 之含義，這和我們今天講座所介紹的概念是完全不同的。



<!-- # Build systems -->
# 構建系統

<!-- If you write a paper in LaTeX, what are the commands you need to run to
produce your paper? What about the ones used to run your benchmarks,
plot them, and then insert that plot into your paper? Or to compile the
code provided in the class you're taking and then running the tests? -->
如果您使用 LaTeX 來編寫論文，您需要執行哪些命令才能編譯出您想要的論文呢？執行效能測試、繪製圖表然後將其插入論文的命令又有哪些？或者，如何編譯本課程提供的程式碼並執行測試呢？

<!-- For most projects, whether they contain code or not, there is a "build
process". Some sequence of operations you need to do to go from your
inputs to your outputs. Often, that process might have many steps, and
many branches. Run this to generate this plot, that to generate those
results, and something else to produce the final paper. As with so many
of the things we have seen in this class, you are not the first to
encounter this annoyance, and luckily there exist many tools to help
you! -->
對於大多數系統來說，不論其是否包含程式碼，都會包含一個“構建過程”。有時，您需要執行一系列操作。通常，這一過程包含了很多步驟，很多分支。執行一些命令來產生圖表，然後執行另外的一些命令產生結果，然後在執行其他的命令來得到最終的論文。有很多事情需要我們完成，您並不是第一個因此感到苦惱的人，幸運的是，有很多工具可以幫助我們完成這些操作。

<!-- These are usually called "build systems", and there are _many_ of them.
Which one you use depends on the task at hand, your language of
preference, and the size of the project. At their core, they are all
very similar though. You define a number of _dependencies_, a number of
_targets_, and _rules_ for going from one to the other. You tell the
build system that you want a particular target, and its job is to find
all the transitive dependencies of that target, and then apply the rules
to produce intermediate targets all the way until the final target has
been produced. Ideally, the build system does this without unnecessarily
executing rules for targets whose dependencies haven't changed and where
the result is available from a previous build. -->
這些工具通常被稱為"構建系統"，而且這些工具還不少。如何選擇工具完全取決於您當前手頭上要完成的任務以及項目的規模。從本質上講，這些工具都是非常類似的。您需要定義*依賴*、*目標*和*規則*。您必須告訴構建系統您具體的構建目標，系統的任務則是找到構建這些目標所需要的依賴，並根據規則構建所需的中間產物，直到最終目標被構建出來。理想的情況下，如果目標的依賴沒有發生改動，並且我們可以從之前的構建中復用這些依賴，那麼與其相關的構建規則並不會被執行。

<!-- `make` is one of the most common build systems out there, and you will
usually find it installed on pretty much any UNIX-based computer. It has
its warts, but works quite well for simple-to-moderate projects. When
you run `make`, it consults a file called `Makefile` in the current
directory. All the targets, their dependencies, and the rules are
defined in that file. Let's take a look at one: -->
`make` 是最常用的構建系統之一，您會發現它通常已被安裝到在所有基於UNIX的系統中。`make`並不完美，但是對於中小型項目來說，它已經足夠好了。當您執行`make` 時，它會去參考當前目錄下名為`Makefile` 的檔案。所有構建目標、相關依賴和規則都需要在該檔案中定義，它看上去是這樣的：

```make
paper.pdf: paper.tex plot-data.png
	pdflatex paper.tex

plot-%.png: %.dat plot.py
	./plot.py -i $*.dat -o $@
```

<!-- Each directive in this file is a rule for how to produce the left-hand
side using the right-hand side. Or, phrased differently, the things
named on the right-hand side are dependencies, and the left-hand side is
the target. The indented block is a sequence of programs to produce the
target from those dependencies. In `make`, the first directive also
defines the default goal. If you run `make` with no arguments, this is
the target it will build. Alternatively, you can run something like
`make plot-data.png`, and it will build that target instead. -->
這個檔案中的指令，即如何使用右側檔案構建左側檔案的規則。或者，換句話說，引號左側的是構建目標，引號右側的是構建它所需的依賴。縮排的部分是從依賴構建目標時需要用到的程式。在`make` 中，第一條指令還指明了構建的目的，如果您使用不帶參數的`make`，這便是我們最終的構建結果。或者，您可以使用這樣的命令來構建其他目標：`make plot-data.png`。

<!-- The `%` in a rule is a "pattern", and will match the same string on the
left and on the right. For example, if the target `plot-foo.png` is
requested, `make` will look for the dependencies `foo.dat` and
`plot.py`. Now let's look at what happens if we run `make` with an empty
source directory. -->
規則中的`%` 是一種模式，它會匹配其左右兩側相同的字符串。例如，如果目標是`plot-foo.png`， `make` 會去尋找`foo.dat` 和`plot.py` 作為依賴。現在，讓我們看看如果在一個空的原始碼目錄中執行`make` 會發生什麼？

```console
$ make
make: *** No rule to make target 'paper.tex', needed by 'paper.pdf'.  Stop.
```

<!-- `make` is helpfully telling us that in order to build `paper.pdf`, it
needs `paper.tex`, and it has no rule telling it how to make that file.
Let's try making it! -->
`make` 會告訴我們，為了構建出`paper.pdf`，它需要`paper.tex`，但是並沒有一條規則能夠告訴它如何構建該檔案。讓我們構建它吧！

```console
$ touch paper.tex
$ make
make: *** No rule to make target 'plot-data.png', needed by 'paper.pdf'.  Stop.
```
<!-- 
Hmm, interesting, there _is_ a rule to make `plot-data.png`, but it is a
pattern rule. Since the source files do not exist (`foo.dat`), `make`
simply states that it cannot make that file. Let's try creating all the
files: -->
有趣的是，我們是**有**構建`plot-data.png` 的規則的，但是這是一條模式規則。因為原始檔案`foo.dat` 並不存在，因此`make` 就會告訴您它不能構建`plot-data.png`，讓我們創建這些檔案：

```console
$ cat paper.tex
\documentclass{article}
\usepackage{graphicx}
\begin{document}
\includegraphics[scale=0.65]{plot-data.png}
\end{document}
$ cat plot.py
#!/usr/bin/env python
import matplotlib
import matplotlib.pyplot as plt
import numpy as np
import argparse

parser = argparse.ArgumentParser()
parser.add_argument('-i', type=argparse.FileType('r'))
parser.add_argument('-o')
args = parser.parse_args()

data = np.loadtxt(args.i)
plt.plot(data[:, 0], data[:, 1])
plt.savefig(args.o)
$ cat data.dat
1 1
2 2
3 3
4 4
5 8
```

<!-- Now what happens if we run `make`? -->
當我們執行`make` 時會發生什麼事？

```console
$ make
./plot.py -i data.dat -o plot-data.png
pdflatex paper.tex
... lots of output ...
```

<!-- And look, it made a PDF for us!
What if we run `make` again? -->
看！它幫我們產生了PDF！
如果再次執行 `make` 會怎樣？

```console
$ make
make: 'paper.pdf' is up to date.
```

<!-- It didn't do anything! Why not? Well, because it didn't need to. It
checked that all of the previously-built targets were still up to date
with respect to their listed dependencies. We can test this by modifying
`paper.tex` and then re-running `make`: -->
什麼事情都沒做！為什麼？好吧，因為它什麼都不需要做。make會去檢查之前的依賴才決定是否需要再次構建。讓我們試試修改`paper.tex` 並重新執行`make`：

```console
$ vim paper.tex
$ make
pdflatex paper.tex
...
```
<!-- 
Notice that `make` did _not_ re-run `plot.py` because that was not
necessary; none of `plot-data.png`'s dependencies changed! -->
注意 `make` 並**沒有**重新構建 `plot.py` ，因為沒必要。`plot-data.png` 的所有依賴都沒有發生改變。

<!-- # Dependency management -->
# 依賴管理

<!-- At a more macro level, your software projects are likely to have
dependencies that are themselves projects. You might depend on installed
programs (like `python`), system packages (like `openssl`), or libraries
within your programming language (like `matplotlib`). These days, most
dependencies will be available through a _repository_ that hosts a
large number of such dependencies in a single place, and provides a
convenient mechanism for installing them. Some examples include the
Ubuntu package repositories for Ubuntu system packages, which you access
through the `apt` tool, RubyGems for Ruby libraries, PyPi for Python
libraries, or the Arch User Repository for Arch Linux user-contributed
packages. -->
更宏觀來說，你的項目中的依賴可能本身也是其他的項目。您也許會依賴某些程式(例如`python`)、系統套件(例如`openssl`)或相關程式語言的函式庫(例如`matplotlib`)。現在，大多數的依賴可以通過某些**軟體倉庫(repository)**來獲取，這些倉庫會在一個地方託管大量的依賴，我們則可以通過一套非常簡單的機制來安裝他們。例如 Ubuntu 系統下面有 Ubuntu 套件庫，您可以通過 `apt` 這個工具來訪問， RubyGems 則包含了 Ruby 的相關套件，PyPi 包含了 Python 套件庫，Arch Linux 用戶貢獻的套件庫則可以在 Arch User Repository 中找到。

<!-- Since the exact mechanisms for interacting with these repositories vary
a lot from repository to repository and from tool to tool, we won't go
too much into the details of any specific one in this lecture. What we
_will_ cover is some of the common terminology they all use. The first
among these is _versioning_. Most projects that other projects depend on
issue a _version number_ with every release. Usually something like
8.1.3 or 64.1.20192004. They are often, but not always, numerical.
Version numbers serve many purposes, and one of the most important of
them is to ensure that software keeps working. Imagine, for example,
that I release a new version of my library where I have renamed a
particular function. If someone tried to build some software that
depends on my library after I release that update, the build might fail
because it calls a function that no longer exists! Versioning attempts
to solve this problem by letting a project say that it depends on a
particular version, or range of versions, of some other project. That
way, even if the underlying library changes, dependent software
continues building by using an older version of my library. -->
由於每個倉庫、每種工具的運行機制都不太一樣，因此我們並不會在本節課深入講解其細節。我們會介紹一些通用的術語，例如*版本控制*。大多數被其他項目所依賴的項目都會在每次發布新版本時創建一個*版本號*。通常看上去像 8.1.3 或 64.1.20192004 。版本號一般是數字構成的，但也並不絕對。版本號有很多用途，其中最重要的作用是保證軟體能夠運行。試想一下，假如我的套件要發布一個新版本，在這個版本中我重命名了某個函數。如果有人在我的套件升級版本後，仍希望基於它構建新的軟體，那麼很可能構建會失敗，因為它希望呼叫的函數已經不存在了。有了版本控制就可以很好的解決這個問題，我們可以指定當前項目需要基於某個版本，甚至某個範圍內的版本，或是某些項目來構建。這麼做的話，即使某個相依套件發生了變化，依賴它的軟體仍可基於其之前的版本進行構建。

<!-- That also isn't ideal though! What if I issue a security update which
does _not_ change the public interface of my library (its "API"), and
which any project that depended on the old version should immediately
start using? This is where the different groups of numbers in a version
come in. The exact meaning of each one varies between projects, but one
relatively common standard is [_semantic versioning_](https://semver.org/).
With semantic versioning, every version number is of the form: major.minor.patch. The rules are: -->
這樣還並不理想！如果我們發布了一項和安全相關的升級，它並*沒有*影響到任何公開接口（API），但是處於安全的考慮，依賴它的項目都應該立即升級，那應該怎麼做呢？這也是版本號包含多個部分的原因。不同項目所用的版本號其具體含義並不完全相同，但是一個相對比較常用的標準是[語義版本號](https://semver.org/)，這種版本號具有不同的語義，它的格式是這樣的：主版本號.次版本號.補丁號。相關規則有：

 <!-- - If a new release does not change the API, increase the patch version.
 - If you _add_ to your API in a backwards-compatible way, increase the
   minor version.
 - If you change the API in a non-backwards-compatible way, increase the
   major version. -->
 - 如果新的版本沒有改變 API ，請將補丁號遞增；
 - 如果您添加了 API 並且該更動是向下相容的，請將次版本號遞增；
 - 如果您修改了 API 但是它並不向下相容，請將主版本號遞增。

<!-- This already provides some major advantages. Now, if my project depends
on your project, it _should_ be safe to use the latest release with the
same major version as the one I built against when I developed it, as
long as its minor version is at least what it was back then. In other
words, if I depend on your library at version `1.3.7`, then it _should_
be fine to build it with `1.3.8`, `1.6.1`, or even `1.3.0`. Version
`2.2.4` would probably not be okay, because the major version was
increased. We can see an example of semantic versioning in Python's
version numbers. Many of you are probably aware that Python 2 and Python
3 code do not mix very well, which is why that was a _major_ version
bump. Similarly, code written for Python 3.5 might run fine on Python
3.7, but possibly not on 3.4. -->
這麼做有很多好處。現在如果我們的項目是基於您的項目構建的，那麼只要最新版本的主版本號只要沒變就是安全的，次版本號不低於之前我們使用的版本即可。換句話說，如果我依賴的版本是 `1.3.7` ，那麼使用 `1.3.8` 、`1.6.1` ，甚至是 `1.3.0` 都是可以的。如果版本號是 `2.2.4` 就不一定能用了，因為它的主版本號增加了。我們可以將Python 的版本號作為語義版本號的一個實例。您應該知道，Python 2 和 Python 3 的程式碼是不相容的，這也是為什麼 Python 的主版本號改變的原因。類似的，使用 Python 3.5 編寫的程式碼在 3.7 上可以運行，但是在 3.4 上可能會不行。

<!-- When working with dependency management systems, you may also come
across the notion of _lock files_. A lock file is simply a file that
lists the exact version you are _currently_ depending on of each
dependency. Usually, you need to explicitly run an update program to
upgrade to newer versions of your dependencies. There are many reasons
for this, such as avoiding unnecessary recompiles, having reproducible
builds, or not automatically updating to the latest version (which may
be broken). An extreme version of this kind of dependency locking is
_vendoring_, which is where you copy all the code of your dependencies
into your own project. That gives you total control over any changes to
it, and lets you introduce your own changes to it, but also means you
have to explicitly pull in any updates from the upstream maintainers
over time. -->
使用依賴管理系統的時候，您可能會遇到鎖文件（_lock files_）這一概念。鎖文件列出了您當前每個依賴所對應的具體版本號。通常，您需要執行升級程序才能更新依賴的版本。這麼做的原因有很多，例如避免不必要的重新編譯、可重複創建的軟體版本或禁止自動升級到最新版本（可能會包含bug）。還有一種極端的依賴鎖定叫做*vendoring*，它會把您的依賴中的所有程式碼直接拷貝到您的項目中，這樣您就能夠完全掌控程式碼的任何修改，同時您也可以將自己的修改添加進去，不過這也意味著如何該依賴的維護者更新了某些程式碼，您也必須要自己去拉取這些更新。

<!-- # Continuous integration systems -->
# 持續整合系統

<!-- As you work on larger and larger projects, you'll find that there are
often additional tasks you have to do whenever you make a change to it.
You might have to upload a new version of the documentation, upload a
compiled version somewhere, release the code to pypi, run your test
suite, and all sort of other things. Maybe every time someone sends you
a pull request on GitHub, you want their code to be style checked and
you want some benchmarks to run? When these kinds of needs arise, it's
time to take a look at continuous integration. -->
隨著您接觸到的項目規模越來越大，您會發現修改程式碼之後還有很多額外的工作要做。您可能需要上傳一份新版本的文檔、上傳編譯後的文件到某處、發布程式碼到pypi，執行測試套件等等。或許您希望每次有人提交程式碼到GitHub 的時候，他們的程式碼風格被檢查過並執行過某些基準測試？如果您有這方面的需求，那麼請花些時間了解一下持續整合。

<!-- Continuous integration, or CI, is an umbrella term for "stuff that runs
whenever your code changes", and there are many companies out there that
provide various types of CI, often for free for open-source projects.
Some of the big ones are Travis CI, Azure Pipelines, and GitHub Actions.
They all work in roughly the same way: you add a file to your repository
that describes what should happen when various things happen to that
repository. By far the most common one is a rule like "when someone
pushes code, run the test suite". When the event triggers, the CI
provider spins up a virtual machines (or more), runs the commands in
your "recipe", and then usually notes down the results somewhere. You
might set it up so that you are notified if the test suite stops
passing, or so that a little badge appears on your repository as long as
the tests pass. -->
持續整合，或者叫做 CI 是一種雨傘術語（umbrella term），它指的是那些“當您的程式碼變動時，自動運行的東西”，市場上有很多提供各式各樣 CI 工具的公司，這些工具大部分都是免費或開源的。比較大的有Travis CI、Azure Pipelines 和 GitHub Actions。它們的工作原理都是類似的：您需要在程式碼倉庫中添加一個文件，描述當前倉庫發生任何修改時，應該如何應對。目前為止，最常見的規則是：如果有人提交程式碼，執行測試套件。當這個事件被觸發時，CI 提供方會啟動一個（或多個）虛擬機，執行您制定的規則，並且通常會記錄下相關的執行結果。您可以進行某些設置，這樣當測試套件失敗時您能夠收到通知或者當測試全部通過時，您的倉庫主頁會顯示一個徽章。

<!-- As an example of a CI system, the class website is set up using GitHub
Pages. Pages is a CI action that runs the Jekyll blog software on every
push to `master` and makes the built site available on a particular
GitHub domain. This makes it trivial for us to update the website! We
just make our changes locally, commit them with git, and then push. CI
takes care of the rest. -->
本課程的網站基於 GitHub Pages 構建，這就是一個很好的例子。Pages 在每次 `master` 有程式碼更新時，會執行 Jekyll 靜態網站生成器，然後使您的網站可以通過某個 GitHub 域名來訪問。對於我們來說這些事情太瑣碎了，我現在我們只需要在本地進行修改，然後使用 git 提交程式碼，發佈到遠端。CI 會自動幫我們處理後續的事情。

<!-- ## A brief aside on testing -->
## 測試簡介

<!-- Most large software projects come with a "test suite". You may already
be familiar with the general concept of testing, but we thought we'd
quickly mention some approaches to testing and testing terminology that
you may encounter in the wild: -->
多數的大型軟體都有“測試套件”。您可能已經對測試的相關概念有所了解，但是我們覺得有些測試方法和測試術語還是應該再次提醒一下：

 <!-- - Test suite: a collective term for all the tests
 - Unit test: a "micro-test" that tests a specific feature in isolation
 - Integration test: a "macro-test" that runs a larger part of the
   system to check that different feature or components work _together_.
 - Regression test: a test that implements a particular pattern that
   _previously_ caused a bug to ensure that the bug does not resurface.
 - Mocking: the replace a function, module, or type with a fake
   implementation to avoid testing unrelated functionality. For example,
   you might "mock the network" or "mock the disk". -->
 - 測試套件：所有測試的統稱。
 - 單元測試：一個“微觀測試”，用於對某個封裝的特性進行測試。
 - 集成測試：一個“宏觀測試”，針對系統的某一大部分進行，測試其不同的特性或組件是否能*協同*工作。
 - 回歸測試：用於保證之前引起問題的 bug 不會再次出現。
 - 模擬（Mocking）：使用一個假的實現來替換函數、模組或類別，屏蔽那些和測試不相關的內容。例如，您可能會“模擬網路連接” 或“模擬硬碟”

<!-- # Exercises -->
# 課後練習 

<!-- 1. Most makefiles provide a target called `clean`. This isn't intended
     to produce a file called `clean`, but instead to clean up any files that can be re-built by make. Think of it as a way to "undo" all of the build steps. Implement a `clean` target for the `paper.pdf` `Makefile` above. You will have to make the target [phony](https://www.gnu.org/software/make/manual/html_node/Phony-Targets.html). You may find the [`git
          ls-files`](https://git-scm.com/docs/git-ls-files) subcommand useful.
    A number of other very common make targets are listed
    [here](https://www.gnu.org/software/make/manual/html_node/Standard-Targets.html#Standard-Targets). -->
1. 大多數的 makefiles 都提供了一個名為 `clean` 的構建目標，這並不是說我們會生成一個名為 `clean` 的文件，而是我們可以使用它清理文件，讓 make 重新構建。您可以理解為它的作用是“撤銷”所有構建步驟。在上面的makefile 中為 `paper.pdf` 實現一個 `clean` 目標。您需要構建 [phony](https://www.gnu.org/software/make/manual/html_node/Phony-Targets.html)。您也許會發現 [`git ls-files`](https://git-scm.com/docs/git-ls-files) 子命令很有用。其他一些有用的make 構建目標可以在[這裡](https://www.gnu.org/software/make/manual/html_node/Standard-Targets.html#Standard-Targets)找到。
   
 <!-- 2. Take a look at the various ways to specify version requirements for
    dependencies in [Rust's build
    system](https://doc.rust-lang.org/cargo/reference/specifying-dependencies.html).
    Most package repositories support similar syntax. For each one
    (caret, tilde, wildcard, comparison, and multiple), try to come up
    with a use-case in which that particular kind of requirement makes
    sense. -->
2. 指定版本要求的方法很多，讓我們學習一下[Rust的構建系統](https://doc.rust-lang.org/cargo/reference/specifying-dependencies.html)的依賴管理。大多數的包管理倉庫都支持類似的語法。對於每種語法(插入符、波浪號、萬用字元、比較、乘積)，構建一種場景使其具有實際意義。
 <!-- 3. Git can act as a simple CI system all by itself. In `.git/hooks`
    inside any git repository, you will find (currently inactive) files
    that are run as scripts when a particular action happens. Write a
    [`pre-commit`](https://git-scm.com/docs/githooks#_pre_commit) hook
    that runs `make paper.pdf` and refuses the commit if the `make`
    command fails. This should prevent any commit from having an
    unbuildable version of the paper. -->
3. Git 可以作為一個簡單的 CI 系統來使用，在任何 git 倉庫中的`.git/hooks` 目錄中，您可以找到一些文件（當前處於未啟用狀態），它們的作用和腳本一樣，當某些事件發生時便可以自動執行。請編寫一個 [`pre-commit`](https://git-scm.com/docs/githooks#_pre_commit) hook，當執行 `make` 命令失敗後，它會執行 `make paper.pdf` 並拒絕您的提交。這樣做可以避免產生包含不可構建版本的提交信息。
 <!-- 4. Set up a simple auto-published page using [GitHub
    Pages](https://help.github.com/en/actions/automating-your-workflow-with-github-actions).
    Add a [GitHub Action](https://github.com/features/actions) to the
    repository to run `shellcheck` on any shell files in that
    repository (here is [one way to do
    it](https://github.com/marketplace/actions/shellcheck)). Check that
    it works! -->
4. 基於 [GitHub Pages](https://help.github.com/en/actions/automating-your-workflow-with-github-actions) 創建任意一個可以自動發布的頁面。添加一個 [GitHub Action](https://github.com/features/actions) 到該倉庫，對倉庫中的所有shell 文件執行`shellcheck` ([方法之一](https://github.com/marketplace/actions/shellcheck))。
 <!-- 5. [Build your own](https://help.github.com/en/actions/automating-your-workflow-with-github-actions/building-actions)
    GitHub action to run [`proselint`](http://proselint.com/) or
    [`write-good`](https://github.com/btford/write-good) on all the
    `.md` files in the repository. Enable it in your repository, and
    check that it works by filing a pull request with a typo in it. -->
5. [構建屬於您的](https://help.github.com/en/actions/automating-your-workflow-with-github-actions/building-actions) GitHub action，對倉庫中所有的 `.md ` 文件執行 [`proselint`](http://proselint.com/) 或 [`write-good`](https://github.com/btford/write-good)，在您的倉庫中開啟這一功能，提交一個包含錯誤的文件看看該功能是否生效。
