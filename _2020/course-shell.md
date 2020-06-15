---
layout: lecture
title: "課程概覽 + shell"
date: 2019-01-13
ready: true
video:
  aspect: 56.25
  id: Z56Jmr9Z34Q
---

<!-- # Motivation -->

# 動機

<!-- As computer scientists, we know that computers are great at aiding in
repetitive tasks. However, far too often, we forget that this applies
just as much to our _use_ of the computer as it does to the computations
we want our programs to perform. We have a vast range of tools
available at our fingertips that enable us to be more productive and
solve more complex problems when working on any computer-related
problem. Yet many of us utilize only a small fraction of those tools; we
only know enough magical incantations by rote to get by, and blindly
copy-paste commands from the internet when we get stuck. -->
作爲電腦科學家，我們清楚電腦是處理重複作業的好工具，然而我們常常忘記，這不止是指使用程式計算，_使用_ 電腦本身也是如此。
處理電腦問題的時候，我們有許多易於獲取的工具來提升效率與解決更複雜的問題，
但是往往只用到了它們的一小部分功能：我們僅僅記住了一些小技巧，或者在遇到困難時隨意在網路上抄寫指令。


<!-- This class is an attempt to address this. -->
希望此課程可以協助你解決以上所提及的問題。

<!-- We want to teach you how to make the most of the tools you know, show
you new tools to add to your toolbox, and hopefully instill in you some
excitement for exploring (and perhaps building) more tools on your own.
This is what we believe to be the missing semester from most Computer
Science curricula. -->
我們將講授如何使用你熟知的工具的全部功能，或為你提供全新的選擇，並且希望能
引起你探索（也許還有開發）更多工具的興趣。我們認為這正是如今電腦課程所缺少的內容。

<!-- # Class structure -->
# 課程結構


<!-- The class consists of 11 1-hour lectures, each one centering on a
[particular topic](/2020/). The lectures are largely independent,
though as the semester goes on we will presume that you are familiar
with the content from the earlier lectures. We have lecture notes
online, but there will be a lot of content covered in class (e.g. in the
form of demos) that may not be in the notes. We will be recording
lectures and posting the recordings online. -->

此課程含有十一節約一小時的課程，每一個講座都會注重在[特定主題](/2020/)上。
儘管這些講座大體上是獨立的，但是我們推定你對之前課程的內容已經熟悉。
我們提供線上的課程講義，但是許多內容僅有課上會涉及（比如demo）。
我們將會提供線上課程回放。


<!-- We are trying to cover a lot of ground over the course of just 11 1-hour
lectures, so the lectures are fairly dense. To allow you some time to
get familiar with the content at your own pace, each lecture includes a
set of exercises that guide you through the lecture's key points. After
each lecture, we are hosting office hours where we will be present to
help answer any questions you might have. If you are attending the class
online, you can send us questions at
[missing-semester@mit.edu](mailto:missing-semester@mit.edu). -->
我們盡力在僅有十一小時的課程中講授許多不同領域的內容，所以講座內容會較緊湊。
為了讓你可以以自己適應的速度熟悉內容，每次講座都包含了一組練習來引導你掌握講座重點。
每節課後，我們會安排時間回答問題。若你在線上參與課程，可以將問題發送至[missing-semester@mit.edu](mailto:missing-semester@mit.edu)。

<!-- Due to the limited time we have, we won't be able to cover all the tools
in the same level of detail a full-scale class might. Where possible, we
will try to point you towards resources for digging further into a tool
or topic, but if something particularly strikes your fancy, don't
hesitate to reach out to us and ask for pointers! -->
由於時間有限，我們無法如同專門課程一樣涵蓋所有內容。
我們會盡力介紹可以幫助你進一步探究工具或特定主題的資源。
如果您有特別注重或感興趣的主題，請隨時與我們聯絡並尋求指導！


# Topic 1: The Shell

<!-- ## What is the shell? -->
## Shell 是什麼？

<!-- Computers these days have a variety of interfaces for giving them
commands; fancyful graphical user interfaces, voice interfaces, and
even AR/VR are everywhere. These are great for 80% of use-cases, but
they are often fundamentally restricted in what they allow you to do —
you cannot press a button that isn't there or give a voice command that
hasn't been programmed. To take full advantage of the tools your
computer provides, we have to go old-school and drop down to a textual
interface: The Shell. -->
如今，電腦有各種介面來讓我們輸入指令：精美的圖形介面，語音聽寫輸入，甚至是AR/VR介面已經無處不在。
這些介面在80%的應用場景運作良好，但從根本上限制了你能做的事情————你無法點選不存在的按鈕或者用語音輸入從未錄入的指令。
為了充分利用電腦的功能，我們需要回到傳統方式，使用文字介面：Shell。

<!-- Nearly all platforms you can get your hand on has a shell in one form or
another, and many of them have several shells for you to choose from.
While they may vary in the details, at their core they are all roughly
the same: they allow you to run programs, give them input, and inspect
their output in a semi-structured way. -->
近乎所有你能接觸到的平臺都以某種形式支援shell，並且他們中的許多都提供了多種shell介面供你選擇。
雖然細節上有所不同，但它們的核心幾乎都是相同的：它允許你執行程式，為它們提供輸入，並以半結構化的方式檢查輸出。

<!-- In this lecture, we will focus on the Bourne Again SHell, or "bash" for
short. This is one of the most widely used shells, and its syntax is
similar to what you will see in many other shells. To open a shell
_prompt_ (where you can type commands), you first need a _terminal_.
Your device probably shipped with one installed, or you can install one
fairly easily. -->
在本課中，我們將會著重介紹Bourne Again SHell，簡稱 「bash」。
這是使用最廣泛的shell之一，其語法與許多其他shell相似。
開啟shell的 _命令提示字元_（可以鍵入指令的地方），需要先開啟 _終端_ 。
你的裝置通常已經安裝了終端，或者你也可以選擇自己安裝一個，非常容易。


<!-- ## Using the shell -->
## 使用shell

<!-- When you launch your terminal, you will see a _prompt_ that often looks
a little like this: -->
當你開始使用終端時，你會看到一個 _提示字元_，它通常看起來像這樣：

```console
missing:~$ 
```
<!-- 
This is the main textual interface to the shell. It tells you that you
are on the machine `missing` and that your "current working directory",
or where you currently are, is `~` (short for "home"). The `$` tells you
that you are not the root user (more on that later). At this prompt you
can type a _command_, which will then be interpreted by the shell. The
most basic command is to execute a program: -->
這是shell的主要文字介面，它告訴你， 你的主機名稱是 `missing` 並且「目前的工作目錄」，或者說是你目前的位置，是 `~` (表示「home」)。
`$` 符號表示你現在不是 root 使用者 (稍後會介紹)。 在此提示字元中，你可以鍵入 _指令_ , 指令會被shell解析。最簡單的指令就是執行程式：

```console
missing:~$ date
Fri 10 Jan 2020 11:49:31 AM EST
missing:~$ 
```

<!-- Here, we executed the `date` program, which (perhaps unsurprisingly)
prints the current date and time. The shell then asks us for another
command to execute. We can also execute a command with _arguments_: -->
在此，我們執行了 `date` 程式，它（不出所料的）印出了當前的日期與時間。
然後，shell將會等待我們執行其他指令。我們可以執行含有 _引數_ 的指令：

```console
missing:~$ echo hello
hello
```

<!-- In this case, we told the shell to execute the program `echo` with the
argument `hello`. The `echo` program simply prints out its arguments.
The shell parses the command by splitting it by whitespace, and then
runs the program indicated by the first word, supplying each subsequent
word as an argument that the program can access. If you want to provide
an argument that contains spaces or other special characters (e.g., a
directory named "My Photos"), you can either quote the argument with `'`
or `"` (`"My Photos"`), or escape just the relevant characters with `\`
(`My\ Photos`). -->
在本例中，我們告訴shell與引數 `hello` 一同執行 `echo`。
`echo` 程式會將引數列印出來。
shell基於空格對指令進行分詞，然後執行第一個詞指代的程式，同時將後續的詞作為程式可以訪問的引數。
如果你希望傳入的引數中含有空格或者其他特殊字元（比如一個名為「My Photos」的檔案夾），你可以使用`'`
或 `"` 將其引用，或使用跳脫字元 `\` 來處理 (`My\ Photos`)。

<!-- But how does the shell know how to find the `date` or `echo` programs?
Well, the shell is a programming environment, just like Python or Ruby,
and so it has variables, conditionals, loops, and functions (next
lecture!). When you run commands in your shell, you are really writing a
small bit of code that your shell interprets. If the shell is asked to
execute a command that doesn't match one of its programming keywords, it
consults an _environment variable_ called `$PATH` that lists which
directories the shell should search for programs when it is given a
command: -->
但是shell如何知道怎樣找到 `date` 或 `echo` 程式？
實際上，shell是一個開發環境，類似於 Python 或 Ruby，所以它具備變數，條件，迴圈，和函式（下一課講解）。
當你在shell內執行指令時，你實際上在寫一段可以被shell解釋的程式碼。如果shell被要求去執行不是程式關鍵字的指令，它會去查詢 _環境變數_ `$PATH`,
`$PATH` 會指出當shell收取某指令時，尋找對應程式的路徑。

```console
missing:~$ echo $PATH
/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
missing:~$ which echo
/bin/echo
missing:~$ /bin/echo $PATH
/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
```

<!-- When we run the `echo` command, the shell sees that it should execute
the program `echo`, and then searches through the `:`-separated list of
directories in `$PATH` for a file by that name. When it finds it, it
runs it (assuming the file is _executable_; more on that later). We can
find out which file is executed for a given program name using the
`which` program. We can also bypass `$PATH` entirely by giving the
_path_ to the file we want to execute. -->
當我們執行 `echo` 指令時, shell會明白它應該執行 `echo`， 然後它會在 `$PATH` 中基於名字尋找由 `:` 分隔的一系列目錄。
當shell找到程式時，shell會執行它（暫且推定此檔案是 _可執行的_ ，之後會講解）。
我們可以使用 `which` 程式來找出給定程式名對應的是哪個檔案。
我們也可以透過直接指定程式的 _路徑_ 來繞過 `$PATH`。

<!-- ## Navigating in the shell -->
## 在shell中移動

<!-- A path on the shell is a delimited list of directories; separated by `/`
on Linux and macOS and `\` on Windows. On Linux and macOS, the path `/`
is the "root" of the file system, under which all directories and files
lie, whereas on Windows there is one root for each disk partition (e.g.,
`C:\`). We will generally assume that you are using a Linux filesystem
in this class. A path that starts with `/` is called an _absolute_ path.
Any other path is a _relative_ path. Relative paths are relative to the
current working directory, which we can see with the `pwd` command and
change with the `cd` command. In a path, `.` refers to the current
directory, and `..` to its parent directory: -->
shell中的路徑是一組被分隔的目錄。在 Linux 與 macOS 上由 `/` 劃分，在 Windows 上由 `\` 劃分。
在 Linux 與 macOS 上，路徑 `/` 是根目錄，所有目錄與檔案位列於此。在 Windows 上則每個磁碟區內都有一個根目錄（例如 `C:\`）。
我們預設你在此課程中使用Linux檔案系統。
以 `/` 起始的路徑被稱為 _絕對路徑_，其他的路徑被稱為 _相對路徑_ 。
相對路徑是相對於當前工作目錄的路徑。我們可以使用 `pwd` 來檢視當前工作目錄，並且可以使用 `cd` 來改變它。
在路徑中，`.` 代表當前工作目錄，而 `..` 代表其上級目錄。

```console
missing:~$ pwd
/home/missing
missing:~$ cd /home
missing:/home$ pwd
/home
missing:/home$ cd ..
missing:/$ pwd
/
missing:/$ cd ./home
missing:/home$ pwd
/home
missing:/home$ cd missing
missing:~$ pwd
/home/missing
missing:~$ ../../bin/echo hello
hello
```

<!-- Notice that our shell prompt kept us informed about what our current
working directory was. You can configure your prompt to show you all
sorts of useful information, which we will cover in a later lecture. -->
可以注意到shell會一直提示我們當前工作目錄位置。
你可以變更終端設定來顯示各種資訊，我們將會在下一課詳細介紹。

<!-- In general, when we run a program, it will operate in the current
directory unless we tell it otherwise. For example, it will usually
search for files there, and create new files there if it needs to. -->
通常來說，當我們執行程式時，它會在當前目錄執行，除非我們指定了其他目錄。
例如，程式通常會在當前位置搜尋檔案，並且在需要的時候在當前位置建立新檔案。

<!-- To see what lives in a given directory, we use the `ls` command: -->
我們可以使用 `ls` 來檢視當前目錄下有哪些檔案：

```console
missing:~$ ls
missing:~$ cd ..
missing:/home$ ls
missing
missing:/home$ cd ..
missing:/$ ls
bin
boot
dev
etc
home
...
```

<!-- Unless a directory is given as its first argument, `ls` will print the
contents of the current directory. Most commands accept flags and
options (flags with values) that start with `-` to modify their
behavior. Usually, running a program with the `-h` or `--help` flag
(`/?` on Windows) will print some help text that tells you what flags
and options are available. For example, `ls --help` tells us: -->
除非我們在第一個引數指定目錄，`ls` 會印出當前目錄下的內容。
許多指令接受旗標與選項（帶有值的旗標），旗標以 `-` 起始且可以改變指令的行為。
通常，執行程式時使用 `-h` 或 `--help` 旗標（在Windows系統中使用 `/?`），會印出說明文字，來告訴使用者有哪些旗標與選項可用。
例如，`ls --help` 會告訴我們：

```
  -l                         use a long listing format
```

```console
missing:~$ ls -l /home
drwxr-xr-x 1 missing  users  4096 Jun 15  2019 missing
```

<!-- This gives us a bunch more information about each file or directory
present. First, the `d` at the beginning of the line tells us that
`missing` is a directory. Then follow three groups of three characters
(`rwx`). These indicate what permissions the owner of the file
(`missing`), the owning group (`users`), and everyone else respectively
have on the relevant item. A `-` indicates that the given principal does
not have the given permission. Above, only the owner is allowed to
modify (`w`) the `missing` directory (i.e., add/remove files in it). To
enter a directory, a user must have "search" (represented by "execute":
`x`) permissions on that directory (and its parents). To list its
contents, a user must have read (`r`) permissions on that directory. For
files, the permissions are as you would expect. Notice that nearly all
the files in `/bin` have the `x` permission set for the last group,
"everyone else", so that anyone can execute those programs. -->
它可以給出目錄與檔案更加詳細的資訊。
首先，`d` 表示 `missing` 是一個目錄。
其後由三個字元 (`rwx`) 組成的三組字元組，分別表示了檔案擁有者(`missing`)，使用者群組(`users`)，與其他所有人所對其擁有的權限。
`-` 代表該使用者未擁有相應權限。
上面顯示的資訊表示，只有擁有者對 `missing` 檔案夾有寫(`w`)的權限（例如增添與移除其中的檔案）。
為了進入某個檔案夾，使用者必須對其本身與其上級檔案夾擁有「搜尋」（表示為「可執行」：`x`）權限。
而為了列出其所有內容，使用者必須對其擁有讀(`r`)權限。
對於檔案，權限是類似的。
要注意近乎所有在 `/bin` 下的程式在最後一組都有 `x` 權限，意味著所有人都可以執行這些程式。

<!-- Some other handy programs to know about at this point are `mv` (to
rename/move a file), `cp` (to copy a file), and `mkdir` (to make a new
directory). -->
在此階段還有一些其他常見命令需要理解，如 `mv` (移動或更名檔案), `cp` (複製檔案), 和 `mkdir` (建立新檔案夾).

<!-- If you ever want _more_ information about a program's arguments, inputs,
outputs, or how it works in general, give the `man` program a try. It
takes as an argument the name of a program, and shows you its _manual
page_. Press `q` to exit. -->
如果你想瞭解 _更多_ 關於程式輸入輸出或引數的內容，嘗試使用 `man` 程式。
它以一個程式名作為引數，然後展示此程式的 _操作手冊_ ，鍵入 `q` 以退出此操作手冊。

```console
missing:~$ man ls
```

<!-- ## Connecting programs -->
## 連接程式

<!-- In the shell, programs have two primary "streams" associated with them:
their input stream and their output stream. When the program tries to
read input, it reads from the input stream, and when it prints
something, it prints to its output stream. Normally, a program's input
and output are both your terminal. That is, your keyboard as input and
your screen as output. However, we can also rewire those streams! -->
在shell中，程式擁有兩個主要「流」：輸入資料流與輸出資料流。
當程式嘗試獲取資訊時，它們會從輸入資料流中獲取，當程式印出結果時，會將資訊輸出至輸出資料流。
通常情況下，一個程式的輸入輸出流都是你的終端。
或者說，你的鍵盤作為輸入，你的熒幕作為輸出。但是，我們可以重新導向這些流！

<!-- The simplest form of redirection is `< file` and `> file`. These let you
rewire the input and output streams of a program to a file respectively: -->
最簡單的重新導向是 `< file` 與 `> file`。它們可以讓你分別重新導向輸入輸出資料流到檔案：

```console
missing:~$ echo hello > hello.txt
missing:~$ cat hello.txt
hello
missing:~$ cat < hello.txt
hello
missing:~$ cat < hello.txt > hello2.txt
missing:~$ cat hello2.txt
hello
```

<!-- You can also use `>>` to append to a file. Where this kind of
input/output redirection really shines is in the use of _pipes_. The `|`
operator lets you "chain" programs such that the output of one is the
input of another: -->
你也可以使用 `>>` 來向檔案附加內容。這種重新導向的真正亮點在於使用 _管道_ 。
`|` 允許我們將一個程式的輸出 「連接」 另一程式的輸入：

```console
missing:~$ ls -l / | tail -n1
drwxr-xr-x 1 root  root  4096 Jun 20  2019 var
missing:~$ curl --head --silent google.com | grep --ignore-case content-length | cut --delimiter=' ' -f2
219
```

<!-- We will go into a lot more detail about how to take advantage of pipes
in the lecture on data wrangling. -->
我們會在資料預處理一課中講述更多如何利用管道的優勢。

<!-- ## A versatile and powerful tool -->
## 能力全面且力量強大的工具

<!-- On most Unix-like systems, one user is special: the "root" user. You may
have seen it in the file listings above. The root user is above (almost)
all access restrictions, and can create, read, update, and delete any
file in the system. You will not usually log into your system as the
root user though, since it's too easy to accidentally break something.
Instead, you will be using the `sudo` command. As its name implies, it
lets you "do" something "as su" (short for "super user", or "root").
When you get permission denied errors, it is usually because you need to
do something as root. Though make sure you first double-check that you
really wanted to do it that way! -->
在大部分類Unix系統中，有一個使用者非常特殊：「root」。
你也許已經在上面的例子中見過了。
root 使用者（幾乎）不受權限限制，可以建立，讀取，更新，和移除系統中的任何文件。
我們通常不需要以 root 使用者登入系統，因為太容易失誤弄壞什麼。
我們通常使用 `sudo` 指令來替換它。
如同它的名字一樣，它允許你用「su」（「super user」, 或者說 「root」）身份來「做」事情。
當遭遇沒有權限的錯誤時，我們通常需要以 root 身份來執行。
但還請一定多加注意自己確實需要這麼做！

<!-- One thing you need to be root in order to do is writing to the `sysfs` file
system mounted under `/sys`. `sysfs` exposes a number of kernel parameters as
files, so that you can easily reconfigure the kernel on the fly without
specialized tools. **Note that sysfs does not exist on Windows or macOS.** -->
有一件事情是只有 root 使用者才可以做，就是向掛載在 `/sys` 下的 `sysfs` 檔案系統寫入內容。
`sysfs` 以檔案形式展示了許多內核參數(kernel parameters)，所以你可以在系統運行時不藉助工具輕鬆改變系統內核。 
**請注意在 Windows 與 macOS 中沒有這個檔案**

<!-- For example, the brightness of your laptop's screen is exposed through a file
called `brightness` under -->
例如，手提電腦的熒幕亮度展示在 `brightness` 中，它位於

```
/sys/class/backlight
```

<!-- By writing a value into that file, we can change the screen brightness.
Your first instinct might be to do something like: -->
透過對該檔案寫入鍵值，我們可以改變熒幕亮度。你的第一個想法可能是：

```console
$ sudo find -L /sys/class/backlight -maxdepth 2 -name '*brightness*'
/sys/class/backlight/thinkpad_screen/brightness
$ cd /sys/class/backlight/thinkpad_screen
$ sudo echo 3 > brightness
An error occurred while redirecting file 'brightness'
open: Permission denied
```

<!-- This error may come as a surprise. After all, we ran the command with
`sudo`! This is an important thing to know about the shell. Operations
like `|`, `>`, and `<` are done _by the shell_, not by the individual
program. `echo` and friends do not "know" about `|`. They just read from
their input and write to their output, whatever it may be. In the case
above, the _shell_ (which is authenticated just as your user) tries to
open the brightness file for writing, before setting that as `sudo
echo`'s output, but is prevented from doing so since the shell does not
run as root. Using this knowledge, we can work around this: -->
我們已經使用了 `sudo` 來執行指令，卻還是遭遇了一個錯誤！
關於shell，我們需要瞭解一件重要的事：`|`, `>`, 與 `<` 等是 _被 shell_ ，而不是被獨立的程式 _執行_ 的。
`echo` 等程式並不「知道」我們使用了 `|`。它們只是從輸入中獲取資訊並將結果寫入到輸出中。
在這種情況中，_shell_ （權限為當前使用者）在執行 `sudo echo` 前就嘗試打開 brightness 檔案並且寫入。
此時因為不是以 root 使用者執行，我們的操作被拒絕了。
理解這一件事後，我們可以這樣執行：

```console
$ echo 3 | sudo tee brightness
```

<!-- Since the `tee` program is the one to open the `/sys` file for writing,
and _it_ is running as `root`, the permissions all work out. You can
control all sorts of fun and useful things through `/sys`, such as the
state of various system LEDs (your path might be different): -->
因為 `tee` 程式開啟了 `/sys`，而且 _它_ 正以 root 使用者執行，因此權限運作正常。
我們可以在 `/sys` 下做有趣又有用的事情了，例如改變系統 LED 的狀態（你的路徑可能會不一樣）：

```console
$ echo 1 | sudo tee /sys/class/leds/input6::scrolllock/brightness
```

<!-- # Next steps -->
# 下一步

<!-- At this point you know your way around a shell enough to accomplish
basic tasks. You should be able to navigate around to find files of
interest and use the basic functionality of most programs. In the next
lecture, we will talk about how to perform and automate more complex
tasks using the shell and the many handy command-line programs out
there. -->
現在你已經可以使用 shell 完成一些基本任務了。
你應該可以透過shell查詢感興趣的檔案並使用大部分程式的基礎功能。
在下一課中，我們將會談談如何使用 shell 與其他命令列工具完成並自動執行更複雜的任務。


# Exercises

 1. Create a new directory called `missing` under `/tmp`.
 1. Look up the `touch` program. The `man` program is your friend.
 1. Use `touch` to create a new file called `semester` in `missing`.
 1. Write the following into that file, one line at a time:
    ```
    #!/bin/sh
    curl --head --silent https://missing.csail.mit.edu
    ```
    The first line might be tricky to get working. It's helpful to know that
    `#` starts a comment in Bash, and `!` has a special meaning even within
    double-quoted (`"`) strings. Bash treats single-quoted strings (`'`)
    differently: they will do the trick in this case. See the Bash
    [quoting](https://www.gnu.org/software/bash/manual/html_node/Quoting.html)
    manual page for more information.
 1. Try to execute the file, i.e. type the path to the script (`./semester`)
    into your shell and press enter. Understand why it doesn't work by
    consulting the output of `ls` (hint: look at the permission bits of the
    file).
 1. Run the command by explicitly starting the `sh` interpreter, and giving it
    the file `semester` as the first argument, i.e. `sh semester`. Why does
    this work, while `./semester` didn't?
 1. Look up the `chmod` program (e.g. use `man chmod`).
 1. Use `chmod` to make it possible to run the command `./semester` rather than
    having to type `sh semester`. How does your shell know that the file is
    supposed to be interpreted using `sh`? See this page on the
    [shebang](https://en.wikipedia.org/wiki/Shebang_(Unix)) line for more
    information.
 1. Use `|` and `>` to write the "last modified" date output by
    `semester` into a file called `last-modified.txt` in your home
    directory.
 1. Write a command that reads out your laptop battery's power level or your
    desktop machine's CPU temperature from `/sys`. Note: if you're a macOS
    user, your OS doesn't have sysfs, so you can skip this exercise.
