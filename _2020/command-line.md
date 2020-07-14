---
layout: lecture
title: "命令列環境"
date: 2019-01-21
ready: true
video:
  aspect: 56.25
  id: e8BO_dYxk5c
---

<!-- In this lecture we will go through several ways in which you can improve your workflow when using the shell. We have been working with the shell for a while now, but we have mainly focused on executing different commands. We will now see how to run several processes at the same time while keeping track of them, how to stop or pause a specific process and how to make a process run in the background.
-->

當我們使用 shell 時，可以使用一些方法改善我們的作業流程，本節課我們就來討論這些方法。 我們已經使用 shell 一段時間了，但是到目前為止我們的關注點主要集中在使用不同的指令上面。現在，我們將會學習如何同時執行多個不同的行程(processes)並追蹤它們的狀態、如何停止或暫停某個行程，以及如何使其在背景執行。

<!--
We will also learn about different ways to improve your shell and other tools, by defining aliases and configuring them using dotfiles. Both of these can help you save time, e.g. by using the same configurations in all your machines without having to type long commands. We will look at how to work with remote machines using SSH. -->

我們還將學習一些方法能夠改善我們的 shell 及其他工具的作業流程，像是透過定義別名或修改配置文件。這些方法都可以幫我們節省大量的時間，僅需要執行一些簡單的指令，我們就可以在所有的主機上使用相同的配置。我們還會學習如何使用 SSH 操作遠端電腦。

<!-- # Job Control -->
# 任務控制

<!-- In some cases you will need to interrupt a job while it is executing, for instance if a command is taking too long to complete (such as a `find` with a very large directory structure to search through).
Most of the time, you can do `Ctrl-C` and the command will stop.
But how does this actually work and why does it sometimes fail to stop the process? -->

某些情況下我們需要中斷正在執行的任務，比如當一個指令需要執行很長時間才能完成時（假設我們在一個非常大的目錄結構中使用 `find` 進行搜索）。大多數情況下，我們可以使用 `Ctrl-C` 來停止指令的執行。但是它的工作原理是什麼呢？為什麼有的時候會無法結束行程？

<!-- ## Killing a process -->
# 結束行程

<!-- Your shell is using a UNIX communication mechanism called a _signal_ to communicate information to the process. When a process receives a signal it stops its execution, deals with the signal and potentially changes the flow of execution based on the information that the signal delivered. For this reason, signals are _software interrupts_. -->

我們的 shell 會使用 UNIX 提供的信號(signal)機制達到行程間的相互溝通。當一個行程接收到信號時，它會停止執行、處理該信號並根據信號傳遞的訊息來改變其執行流程。就這一點而言，信號是一種軟體中斷(software interrupts)。

<!-- In our case, when typing `Ctrl-C` this prompts the shell to deliver a `SIGINT` signal to the process. -->
在上面的例子中，當我們輸入 `Ctrl-C` 時，shell 會發送一個SIGINT 信號到行程。

<!-- Here's a minimal example of a Python program that captures `SIGINT` and ignores it, no longer stopping. To kill this program we can now use the `SIGQUIT` signal instead, by typing `Ctrl-\`. -->
下面這個 Python 程式示範如何捕獲信號 `SIGINT` 並忽略它的基本操作， `Ctrl-C` 並不會讓程式停止。為了停止這個程式，我們需要使用 `SIGQUIT` 信號，通過輸入 `Ctrl-\` 可以發送該信號。


```python
#!/usr/bin/env python
import signal, time

def handler(signum, time):
    print("\nI got a SIGINT, but I am not stopping")

signal.signal(signal.SIGINT, handler)
i = 0
while True:
    time.sleep(.1)
    print("\r{}".format(i), end="")
    i += 1
```

<!-- Here's what happens if we send `SIGINT` twice to this program, followed by `SIGQUIT`. Note that `^` is how `Ctrl` is displayed when typed in the terminal. -->
如果我們向這個程式發送兩次 `SIGINT` ，然後再發送一次 `SIGQUIT` ，程式會有什麼反應？注意 `^` 是我們在終端輸入 `Ctrl` 時的表示形式：

```
$ python sigint.py
24^C
I got a SIGINT, but I am not stopping
26^C
I got a SIGINT, but I am not stopping
30^\[1]    39913 quit       python sigint.py
```

<!-- While `SIGINT` and `SIGQUIT` are both usually associated with terminal related requests, a more generic signal for asking a process to exit gracefully is the `SIGTERM` signal.
To send this signal we can use the [`kill`](https://www.man7.org/linux/man-pages/man1/kill.1.html) command, with the syntax `kill -TERM <PID>`. -->
`SIGINT` 和 `SIGQUIT` 都常常用來發出和終止程式相關的請求，但有個更加通用、優雅地退出信號的方法是 `SIGTERM` 。為了發出這個信號我們需要使用 [`kill`](https://www.man7.org/linux/man-pages/man1/kill.1.html) 指令, 它的語法是： `kill -TERM <PID>`。

<!-- ## Pausing and backgrounding processes -->
## 行程暫停和背景執行

<!-- Signals can do other things beyond killing a process. For instance, `SIGSTOP` pauses a process. In the terminal, typing `Ctrl-Z` will prompt the shell to send a `SIGTSTP` signal, short for Terminal Stop (i.e. the terminal's version of `SIGSTOP`). -->
信號可以讓行程做其他的事情，而不僅僅是終止它們。例如，`SIGSTOP` 會讓行程暫停。在終端中，輸入 `Ctrl-Z` 會讓 shell 發送 `SIGTSTP` (Terminal Stop)信號。

<!-- We can then continue the paused job in the foreground or in the background using [`fg`](https://www.man7.org/linux/man-pages/man1/fg.1p.html) or [`bg`](http://man7.org/linux/man-pages/man1/bg.1p.html), respectively. -->
我們可以使用 [`fg`](https://www.man7.org/linux/man-pages/man1/fg.1p.html) 或 [`bg`](http://man7.org/linux/man-pages/man1/bg.1p.html) 指令恢復暫停的工作。它們分別表示在前台或背景繼續執行。

<!-- The [`jobs`](https://www.man7.org/linux/man-pages/man1/jobs.1p.html) command lists the unfinished jobs associated with the current terminal session.
You can refer to those jobs using their pid (you can use [`pgrep`](https://www.man7.org/linux/man-pages/man1/pgrep.1.html) to find that out).
More intuitively, you can also refer to a process using the percent symbol followed by its job number (displayed by `jobs`). To refer to the last backgrounded job you can use the `$!` special parameter. -->
[`jobs`](https://www.man7.org/linux/man-pages/man1/jobs.1p.html) 指令會列出當前終端作業中尚未完成的任務。我們可以用 pid 指定這些任務（也可以用 [`pgrep`](https://www.man7.org/linux/man-pages/man1/pgrep.1.html) 找出 pid）。更加符合直覺的操作是我們可以使用百分比符號 + 任務編號（ `jobs` 會打印任務編號）來選取該任務。如果要選擇最近的一個任務，可以使用 `$!` 這一特殊參數。

<!-- One more thing to know is that the `&` suffix in a command will run the command in the background, giving you the prompt back, although it will still use the shell's STDOUT which can be annoying (use shell redirections in that case). -->
還有一件事情需要掌握，那就是指令中的 `&` 後綴可以讓指令在直接在背景執行，這使得我們可以直接在 shell 中繼續做其他操作，不過它此時還是會使用 shell 的標準輸出，這一點有時會比較惱人（這種情況可以使用 shell 重定向處理）。

<!-- To background an already running program you can do `Ctrl-Z` followed by `bg`.
Note that backgrounded processes are still children processes of your terminal and will die if you close the terminal (this will send yet another signal, `SIGHUP`).
To prevent that from happening you can run the program with [`nohup`](https://www.man7.org/linux/man-pages/man1/nohup.1.html) (a wrapper to ignore `SIGHUP`), or use `disown` if the process has already been started.
Alternatively, you can use a terminal multiplexer as we will see in the next section. -->
讓已經在執行的行程轉到背景執行，我們可以輸入 `Ctrl-Z` ，然後緊接著再輸入 `bg` 。請注意，背景的行程仍然是我們的終端行程的子行程，一旦我們關閉終端（會發送另外一個信號 `SIGHUP`），這些背景的行程也會終止。為了防止這種情況發生，我們可以使用 [`nohup`](https://www.man7.org/linux/man-pages/man1/nohup.1.html) (一個用來忽略 `SIGHUP` 的封裝) 來執行程式。針對已經執行的程式，可以使用 `disown` 。除此之外，我們可以使用終端多工器來實現，下一章節我們會進行詳細地探討。

<!-- Below is a sample session to showcase some of these concepts. -->
用一些簡單的指令來示範上述觀念:

```
$ sleep 1000
^Z
[1]  + 18653 suspended  sleep 1000

$ nohup sleep 2000 &
[2] 18745
appending output to nohup.out

$ jobs
[1]  + suspended  sleep 1000
[2]  - running    nohup sleep 2000

$ bg %1
[1]  - 18653 continued  sleep 1000

$ jobs
[1]  - running    sleep 1000
[2]  + running    nohup sleep 2000

$ kill -STOP %1
[1]  + 18653 suspended (signal)  sleep 1000

$ jobs
[1]  + suspended (signal)  sleep 1000
[2]  - running    nohup sleep 2000

$ kill -SIGHUP %1
[1]  + 18653 hangup     sleep 1000

$ jobs
[2]  + running    nohup sleep 2000

$ kill -SIGHUP %2

$ jobs
[2]  + running    nohup sleep 2000

$ kill %2
[2]  + 18745 terminated  nohup sleep 2000

$ jobs

```

<!-- 
A special signal is `SIGKILL` since it cannot be captured by the process and it will always terminate it immediately. However, it can have bad side effects such as leaving orphaned children processes. -->
`SIGKILL` 是一個特殊的信號，它不能被行程捕獲並且會馬上結束該行程。不過這樣做會有一些副作用，例如留下孤兒行程。

<!-- You can learn more about these and other signals [here](https://en.wikipedia.org/wiki/Signal_(IPC)) or typing [`man signal`](https://www.man7.org/linux/man-pages/man7/signal.7.html) or `kill -t`. -->
我們可以輸入 [`man signal`](https://www.man7.org/linux/man-pages/man7/signal.7.html) 或 `kill -t` 來，詳細可參考[here](https://en.wikipedia.org/wiki/Signal_(IPC))。


<!-- # Terminal Multiplexers -->
# 終端多工器 (Terminal Multiplexers)

<!-- When using the command line interface you will often want to run more than one thing at once.
For instance, you might want to run your editor and your program side by side.
Although this can be achieved by opening new terminal windows, using a terminal multiplexer is a more versatile solution. -->
當我們在使用命令列介面時，我們通常會希望同時執行多個任務。舉例來說，我們可以想要同時運行我們的編輯器，並在終端的另外一側執行程序。儘管再打開一個新的終端窗口也能達到目的，使用終端多工器則是一種更好的辦法。

<!-- Terminal multiplexers like [`tmux`](https://www.man7.org/linux/man-pages/man1/tmux.1.html) allow you to multiplex terminal windows using panes and tabs so you can interact with multiple shell sessions.
Moreover, terminal multiplexers let you detach a current terminal session and reattach at some point later in time.
This can make your workflow much better when working with remote machines since it voids the need to use `nohup` and similar tricks. -->
像 [`tmux`](https://www.man7.org/linux/man-pages/man1/tmux.1.html) 這類的終端多工器可以允許我們根據面板和標籤分割出多個終端窗口，這樣我們便可以同時與多個 shell 進行互動。
不僅如此，終端多工使我們可以分離當前終端作業並在將來重新連接。 
這讓我們操作遠端設備時的工作流程大大改善，避免了 `nohup` 和其他類似技巧的使用。

<!-- The most popular terminal multiplexer these days is [`tmux`](https://www.man7.org/linux/man-pages/man1/tmux.1.html). `tmux` is highly configurable and by using the associated keybindings you can create multiple tabs and panes and quickly navigate through them. -->
現在最流行的終端多工器是 [`tmux`](https://www.man7.org/linux/man-pages/man1/tmux.1.html)。 `tmux` 是一個高度可定制的工具，我們可以使用相關快捷鍵建立多個分頁並在它們間切換。

<!-- `tmux` expects you to know its keybindings, and they all have the form `<C-b> x` where that means (1) press `Ctrl+b`, (2) release `Ctrl+b`, and then (3) press `x`. `tmux` has the following hierarchy of objects:
- **Sessions** - a session is an independent workspace with one or more windows
    + `tmux` starts a new session.
    + `tmux new -s NAME` starts it with that name.
    + `tmux ls` lists the current sessions
    + Within `tmux` typing `<C-b> d`  detaches the current session
    + `tmux a` attaches the last session. You can use `-t` flag to specify which

- **Windows** - Equivalent to tabs in editors or browsers, they are visually separate parts of the same session
    + `<C-b> c` Creates a new window. To close it you can just terminate the shells doing `<C-d>`
    + `<C-b> N` Go to the _N_ th window. Note they are numbered
    + `<C-b> p` Goes to the previous window
    + `<C-b> n` Goes to the next window
    + `<C-b> ,` Rename the current window
    + `<C-b> w` List current windows

- **Panes** - Like vim splits, panes let you have multiple shells in the same visual display.
    + `<C-b> "` Split the current pane horizontally
    + `<C-b> %` Split the current pane vertically
    + `<C-b> <direction>` Move to the pane in the specified _direction_. Direction here means arrow keys.
    + `<C-b> z` Toggle zoom for the current pane
    + `<C-b> [` Start scrollback. You can then press `<space>` to start a selection and `<enter>` to copy that selection.
    + `<C-b> <space>` Cycle through pane arrangements. -->
`tmux` 的快捷鍵需要我們掌握，它們都是類似 `<C-b> x` 這樣的組合，即先按下`Ctrl+b`，鬆開後再按下 `x` 。 `tmux` 中對象的繼承結構如下：
- **會話** - 每個會話都是一個獨立的工作區，其中包含一個或多個窗口
    + `tmux` 開始一個新會話
    + `tmux new -s NAME` 以指定名稱開始一個新會話
    + `tmux ls` 列出當前所有會話
    + 在 `tmux` 後輸入 `<C-b> d` 來分離該會話
    + `tmux a` 回復最後一個連接的會話， 可以使用 `-t` 來指定哪一個會話

- **窗口** - 相當於編輯器或是瀏覽器中的分頁，從視覺上將一個會話分割為多個部分
    + `<C-b> c` 創建一個新的窗口，使用 `<C-d>` 關閉
    + `<C-b> N` 切換到第 _N_ 個窗口， 每個窗口都是有編號的
    + `<C-b> p` 切換到前一個窗口
    + `<C-b> n` 切換到下一個窗口
    + `<C-b> ,` 重命名當前窗口
    + `<C-b> w` 列出當前所有窗口

- **面板** - 像 vim 中的分屏一樣，面板使我們可以在一個屏幕裡顯示多個 shell
    + `<C-b> "` 水平分割
    + `<C-b> %` 垂直分割
    + `<C-b> <direction>` 使用方向鍵切換到不同面板
    + `<C-b> z` 縮小/放大當前面板
    + `<C-b> [` 開始往回捲動屏幕。我們可以按下空白鍵來開始選擇，Enter鍵複製選中的部分
    + `<C-b> <space>` 切換不同板型


<!-- For further reading,
[here](https://www.hamvocke.com/blog/a-quick-and-easy-guide-to-tmux/) is a quick tutorial on `tmux` and [this](http://linuxcommand.org/lc3_adv_termmux.php) has a more detailed explanation that covers the original `screen` command. You might also want to familiarize yourself with [`screen`](https://www.man7.org/linux/man-pages/man1/screen.1.html), since it comes installed in most UNIX systems. -->
[這裡](https://www.hamvocke.com/blog/a-quick-and-easy-guide-to-tmux/) 是一份 `tmux` 快速入門教學， 而 [這一篇](http://linuxcommand.org/lc3_adv_termmux.php) 文章則更加詳細，它包含了 [`screen`](https://www.man7.org/linux/man-pages/man1/screen.1.html) 命令。我們也許想要掌握 `screen` 命令，因為在大多數 UNIX 系統中都預設安裝有該程序。


<!-- # Aliases -->
# 别名 (Aliases)

<!-- It can become tiresome typing long commands that involve many flags or verbose options.
For this reason, most shells support _aliasing_.
A shell alias is a short form for another command that your shell will replace automatically for you.
For instance, an alias in bash has the following structure: -->
輸入一長串包含許多選項的命令會非常麻煩。
因此，大多數 shell 都支援設置 _別名_。 
shell 的別名相當於一個長命令的縮寫，shell 會自動將其替換成原本的命令。
例如，bash 中的別名語法如下：

```bash
alias alias_name="command_to_alias arg1 arg2"
```

Note that there is no space around the equal sign `=`, because [`alias`](https://www.man7.org/linux/man-pages/man1/alias.1p.html) is a shell command that takes a single argument.

Aliases have many convenient features:
請注意 `=` 兩邊是沒有空格的，因為 [`alias`](https://www.man7.org/linux/man-pages/man1/alias.1p.html) 是一個 shell 命令，它只接受一個參數。 
別名有許多很方便的特性:

```bash
# 創建常用指令的縮寫
alias ll="ls -lh"

# 能夠節省很多的指令輸入
alias gs="git status"
alias gc="git commit"
alias v="vim"

# 讓我們避免誤打命令
alias sl=ls

# 重定義現有命令的預設行為
alias mv="mv -i"           # -i prompts before overwrite
alias mkdir="mkdir -p"     # -p make parent dirs as needed
alias df="df -h"           # -h prints human readable format

# 別名可以組合使用
alias la="ls -A"
alias lla="la -l"

# 前置反斜線來忽略某個別名
\ls
# 或使用unalias來禁用別名
unalias la

# 印出該別名的定義
alias ll
# 將會印出 ll='ls -lh'
```

<!-- Note that aliases do not persist shell sessions by default.
To make an alias persistent you need to include it in shell startup files, like `.bashrc` or `.zshrc`, which we are going to introduce in the next section. -->
值得注意的是，在默認情況下 shell 並不會保存別名。
為了讓別名持續生效，我們需要將配置放進 shell 的啟動文件裡，像是 `.bashrc` 或 `.zshrc`，下一節我們就會講到。

# 配置文件 (Dotfiles)

<!-- Many programs are configured using plain-text files known as _dotfiles_
(because the file names begin with a `.`, e.g. `~/.vimrc`, so that they are
hidden in the directory listing `ls` by default). -->
很多程式的配置都是通過純文本格式的被稱作點文件(dotfile)的配置文件來完成的（之所以稱為點文件，是因為它們的文件名以 `.` 開頭，例如 `~/.vimrc`。也正因為此，它們默認是隱藏文件， `ls` 並不會顯示它們）。

<!-- Shells are one example of programs configured with such files. On startup, your shell will read many files to load its configuration.
Depending on the shell, whether you are starting a login and/or interactive the entire process can be quite complex.
[Here](https://blog.flowblok.id.au/2013-02/shell-startup-scripts.html) is an excellent resource on the topic. -->
shell 的配置也是通過這類文件完成的。在啟動時， shell 程式會讀取很多文件以載入其配置選項。根據 shell 本身的不同，我們從登入開始還是以互動的方式完成這一過程可能會有很大的不同。關於這一話題，[這裡](https://blog.flowblok.id.au/2013-02/shell-startup-scripts.html)有非常好的資源。

<!-- For `bash`, editing your `.bashrc` or `.bash_profile` will work in most systems.
Here you can include commands that you want to run on startup, like the alias we just described or modifications to your `PATH` environment variable.
In fact, many programs will ask you to include a line like `export PATH="$PATH:/path/to/program/bin"` in your shell configuration file so their binaries can be found. -->
對於 `bash` 來說，在大多數系統下，我們可以透過編輯 `.bashrc` 或 `.bash_profile` 來進行配置。在文件中我們可以添加需要在啟動時執行的指令，例如上文我們講到過的別名，或者是我們的環境變量。 實際上，很多程序都要求我們在 shell 的配置文件中包含一行類似 `export PATH="$PATH:/path/to/program/bin"` 的命令，這樣才能確保這些程式能夠被 shell 找到。

<!-- Some other examples of tools that can be configured through dotfiles are: -->
還有一些其他工具可以透過點文件配置:

- `bash` - `~/.bashrc`, `~/.bash_profile`
- `git` - `~/.gitconfig`
- `vim` - `~/.vimrc` 和 `~/.vim` 目錄
- `ssh` - `~/.ssh/config`
- `tmux` - `~/.tmux.conf`

<!-- How should you organize your dotfiles? They should be in their own folder,
under version control, and **symlinked** into place using a script. This has
the benefits of: -->
我們應該如何管理這些配置文件呢，它們應該在它們的文件夾下，並使用版本控制系統進行管理，然後通過腳本將其**符號鏈接(symlinked)**到需要的地方。這麼做有如下好處：

<!-- - **Easy installation**: if you log in to a new machine, applying your
customizations will only take a minute.
- **Portability**: your tools will work the same way everywhere.
- **Synchronization**: you can update your dotfiles anywhere and keep them all
in sync.
- **Change tracking**: you're probably going to be maintaining your dotfiles
for your entire programming career, and version history is nice to have for
long-lived projects. -->
- **安裝簡單**: 如果我們登入了一台新的設備，在這台設備上啟用配置只需要幾分鐘的時間
- **可移植性**: 我們的工具在任何地方都以相同的配置工作 
- **同步**: 在一處更新配置文件，可以同步到其他所有地方 
- **追蹤變更**: 我們可能要在整個工程師生涯中持續維護這些配置文件，而對於長期項目而言，版本歷史是非常重要的

<!-- What should you put in your dotfiles?
You can learn about your tool's settings by reading online documentation or
[man pages](https://en.wikipedia.org/wiki/Man_page). Another great way is to
search the internet for blog posts about specific programs, where authors will
tell you about their preferred customizations. Yet another way to learn about
customizations is to look through other people's dotfiles: you can find tons of
[dotfiles repositories](https://github.com/search?o=desc&q=dotfiles&s=stars&type=Repositories)
on Github --- see the most popular one
[here](https://github.com/mathiasbynens/dotfiles) (we advise you not to blindly
copy configurations though).
[Here](https://dotfiles.github.io/) is another good resource on the topic.

All of the class instructors have their dotfiles publicly accessible on GitHub: [Anish](https://github.com/anishathalye/dotfiles),
[Jon](https://github.com/jonhoo/configs),
[Jose](https://github.com/jjgo/dotfiles). -->
配置文件中需要放些什麼？我們可以通過線上文件和[幫助手冊](https://en.wikipedia.org/wiki/Man_page)了解所使用工具的設置項。另一個方法是在網上搜尋有關特定程序的文章，作者們在文章中會分享他們的配置。還有一種方法就是直接瀏覽其他人的配置文件：我們可以在[這裡](https://github.com/search?o=desc&q=dotfiles&s=stars&type=Repositories)找到無數的dotfiles庫 —— 其中最受歡迎的那些可以在[這裡](https://github.com/mathiasbynens/dotfiles)找到（建議不要直接複製別人的配置）。[這裡](https://dotfiles.github.io/)也有一些非常有用的資源。
本課程的老師們也在 GitHub 上開源了他們的配置文件： [Anish](https://github.com/anishathalye/dotfiles), [Jon](https://github.com/jonhoo/configs), [Jose](https://github.com/jjgo/dotfiles)。

<!-- ## Portability -->
## 可移植性

<!-- A common pain with dotfiles is that the configurations might not work when working with several machines, e.g. if they have different operating systems or shells. Sometimes you also want some configuration to be applied only in a given machine.

There are some tricks for making this easier.
If the configuration file supports it, use the equivalent of if-statements to
apply machine specific customizations. For example, your shell could have something
like: -->
配置文件的一個常見的痛點是它可能並不能在多種設備上生效。例如，如果我們在不同設備上使用的操作系統或者 shell 是不同的，則配置文件是無法生效的。或者，有時我們僅希望特定的配置只在某些設備上生效。 

有一些技巧可以輕鬆達成這些目的。如果配置文件 if 語句，則我們可以藉助它針對不同的設備編寫不同的配置。例如，我們的 shell 可以這樣做：

```bash
if [[ "$(uname)" == "Linux" ]]; then {do_something}; fi

# 使用和 shell 相關的配置時先檢查當前 shell 類型
if [[ "$SHELL" == "zsh" ]]; then {do_something}; fi

# 您也可以針對特定的設備進行配置
if [[ "$(hostname)" == "myServer" ]]; then {do_something}; fi
```

<!-- If the configuration file supports it, make use of includes. For example,
a `~/.gitconfig` can have a setting: -->
如果配置文件支持 include 功能，您也可以多加利用。例如： `~/.gitconfig` 可以這樣編寫：

```
[include]
    path = ~/.gitconfig_local
```

<!-- And then on each machine, `~/.gitconfig_local` can contain machine-specific
settings. You could even track these in a separate repository for
machine-specific settings. -->
然後我們可以在每個設備上建立配置文件 `~/.gitconfig_local` ，包含與該設備相關的特定配置。甚至應該創建一個單獨的 repository 來管理這些與設備相關的配置。

<!-- This idea is also useful if you want different programs to share some configurations. For instance, if you want both `bash` and `zsh` to share the same set of aliases you can write them under `.aliases` and have the following block in both: -->
如果希望在不同的程序之間共享某些配置，該方法也適用。例如，如果您想要在 `bash` 和 `zsh` 中同時啟用一些別名，您可以把它們寫在 `.aliases` 裡，然後在這兩個 shell 裡套用：

```bash
# Test if ~/.aliases exists and source it
if [ -f ~/.aliases ]; then
    source ~/.aliases
fi
```

<!-- # Remote Machines -->
# 遠端連線

<!-- It has become more and more common for programmers to use remote servers in their everyday work. If you need to use remote servers in order to deploy backend software or you need a server with higher computational capabilities, you will end up using a Secure Shell (SSH). As with most tools covered, SSH is highly configurable so it is worth learning about it. -->
對於工程師來說，在他們的日常工作中使用遠端伺服器已經非常普遍。如果需要使用遠端伺服器來部署後端軟體或一些計算能力強大的伺服器，您就會用到Secure Shell（SSH）。和其他工具一樣，SSH 也是可以高度可配置的，也值得我們花時間學習它。

<!-- To `ssh` into a server you execute a command as follows -->
通過以下指令，可以使用 ssh 連接到其他伺服器：

```bash
ssh foo@bar.mit.edu
```

<!-- Here we are trying to ssh as user `foo` in server `bar.mit.edu`.
The server can be specified with a URL (like `bar.mit.edu`) or an IP (something like `foobar@192.168.1.42`). Later we will see that if we modify ssh config file you can access just using something like `ssh bar`. -->
這裡我們嘗試以用戶名 `foo` 登入伺服器 `bar.mit.edu`。伺服器可以通過 URL 指定（例如 `bar.mit.edu`），也可以使用 IP 指定（例如 `foobar@192.168.1.42`）。後面我們會介紹如何修改 ssh 配置文件使我們可以用類似 `ssh bar` 這樣的指令來登陸伺服器。

<!-- ## Executing commands -->
## 執行指令

<!-- An often overlooked feature of `ssh` is the ability to run commands directly.
`ssh foobar@server ls` will execute `ls` in the home folder of foobar.
It works with pipes, so `ssh foobar@server ls | grep PATTERN` will grep locally the remote output of `ls` and `ls | ssh foobar@server grep PATTERN` will grep remotely the local output of `ls`. -->
`ssh` 的一個經常被忽視的特性是它可以直接遠端執行指令。 `ssh foobar@server ls` 可以直接在用foobar的指令下執行 `ls` 指令。想要配合管線來使用也可以， `ssh foobar@server ls | grep PATTERN` 會在本地查詢遠端 `ls` 的輸出而 `ls | ssh foobar@server grep PATTERN` 會在遠端對本地 `ls` 輸出的結果進行查詢。

<!-- ## SSH Keys -->
## SSH 密鑰

<!-- Key-based authentication exploits public-key cryptography to prove to the server that the client owns the secret private key without revealing the key. This way you do not need to reenter your password every time. Nevertheless, the private key (often `~/.ssh/id_rsa` and more recently `~/.ssh/id_ed25519`) is effectively your password, so treat it like so. -->
基於密鑰的驗證機制使用了密碼學中的公鑰，我們只需要向伺服器證明用戶端持有對應的私鑰，而不需要公開其私鑰。這樣就可以避免每次登入都輸入密碼的麻煩了。不過，私鑰(通常是 `~/.ssh/id_rsa` 或者 `~/.ssh/id_ed25519`) 等效於您的密碼，所以一定要好好保存它。

<!-- ### Key generation -->
### 密鑰生成

<!-- To generate a pair you can run [`ssh-keygen`](https://www.man7.org/linux/man-pages/man1/ssh-keygen.1.html). -->
使用 [`ssh-keygen`](https://www.man7.org/linux/man-pages/man1/ssh-keygen.1.html) 命令可以生成一對密鑰：
```bash
ssh-keygen -o -a 100 -t ed25519 -f ~/.ssh/id_ed25519
```
<!-- You should choose a passphrase, to avoid someone who gets hold of your private key to access authorized servers. Use [`ssh-agent`](https://www.man7.org/linux/man-pages/man1/ssh-agent.1.html) or [`gpg-agent`](https://linux.die.net/man/1/gpg-agent) so you do not have to type your passphrase every time. -->
您可以為密鑰設置密碼，防止有人持有您的私鑰並使用它訪問您的伺服器。您可以使用 [`ssh-agent`](https://www.man7.org/linux/man-pages/man1/ssh-agent.1.html) 或 [`gpg-agent`](https://linux.die.net/man/1/gpg-agent) ，這樣就不需要每次都輸入該密碼了。

<!-- If you have ever configured pushing to GitHub using SSH keys, then you have probably done the steps outlined [here](https://help.github.com/articles/connecting-to-github-with-ssh/) and have a valid key pair already. To check if you have a passphrase and validate it you can run `ssh-keygen -y -f /path/to/key`. -->
如果您曾經設定過使用 SSH 密鑰推送到 GitHub，那麼可能您已經完成了[這裡](https://help.github.com/articles/connecting-to-github-with-ssh/)介紹的這些步驟，並且已經有了一對可用的密鑰。要檢查您是否持有密碼並驗證它，您可以運行 `ssh-keygen -y -f /path/to/key`。

<!-- ### Key based authentication -->
## 基於密鑰的認證機制

<!-- `ssh` will look into `.ssh/authorized_keys` to determine which clients it should let in. To copy a public key over you can use: -->
`ssh` 會查詢 `.ssh/authorized_keys` 來確認哪些用戶允許登入。可以通過下面的命令將一個公鑰複製到這裡：

```bash
cat .ssh/id_ed25519.pub | ssh foobar@remote 'cat >> ~/.ssh/authorized_keys'
```

<!-- A simpler solution can be achieved with `ssh-copy-id` where available: -->
如果支援 `ssh-copy-id` 的話，可以使用下面這種更簡單的解決方案：

```bash
ssh-copy-id -i .ssh/id_ed25519.pub foobar@remote
```

<!-- ## Copying files over SSH -->
## 通過 SSH 複製文件

<!-- There are many ways to copy files over ssh: -->
使用 ssh 複製文件有很多方法：

<!-- - `ssh+tee`, the simplest is to use `ssh` command execution and STDIN input by doing `cat localfile | ssh remote_server tee serverfile`. Recall that [`tee`](https://www.man7.org/linux/man-pages/man1/tee.1.html) writes the output from STDIN into a file.
- [`scp`](https://www.man7.org/linux/man-pages/man1/scp.1.html) when copying large amounts of files/directories, the secure copy `scp` command is more convenient since it can easily recurse over paths. The syntax is `scp path/to/local_file remote_host:path/to/remote_file`
- [`rsync`](https://www.man7.org/linux/man-pages/man1/rsync.1.html) improves upon `scp` by detecting identical files in local and remote, and preventing copying them again. It also provides more fine grained control over symlinks, permissions and has extra features like the `--partial` flag that can resume from a previously interrupted copy. `rsync` has a similar syntax to `scp`. -->
- `ssh+tee`, 最簡單的方法是執行 `ssh` 指令，然後通過這樣的方法利用標準輸入實現 `cat localfile | ssh remote_server tee serverfile`。回憶一下，[`tee`](https://www.man7.org/linux/man-pages/man1/tee.1.html) 指令會將標準輸出寫入到一個文件。
- [`scp`](https://www.man7.org/linux/man-pages/man1/scp.1.html) ：當需要拷貝大量的文件或目錄時，使用`scp` 指令則更加方便，因為它可以方便的遍歷相關路徑。語法如下：`scp path/to/local_file remote_host:path/to/remote_file`。
- [`rsync`](https://www.man7.org/linux/man-pages/man1/rsync.1.html) 對 `scp` 進行來改進，它可以檢測本地和遠端的文件以防止重複拷貝。它還可以提供一些諸如符號連接(symlinks)、權限管理等額外功能。甚至還可以基於 `--partial` 旗標實現斷點續傳。 `rsync` 的語法和 `scp` 類似。

<!-- ## Port Forwarding -->
## 端口轉發 (Port Forwarding)

<!-- In many scenarios you will run into software that listens to specific ports in the machine. When this happens in your local machine you can type `localhost:PORT` or `127.0.0.1:PORT`, but what do you do with a remote server that does not have its ports directly available through the network/internet?. -->
很多情況下我們都會遇到軟體需要監聽特定設備的端口。如果是在本機，可以使用 `localhost:PORT` 或 `127.0.0.1:PORT`。但是如果需要監聽遠端伺服器的端口該如何操作呢？這種情況下遠端的端口並不能直接通過網路存取。

<!-- This is called _port forwarding_ and it
comes in two flavors: Local Port Forwarding and Remote Port Forwarding (see the pictures for more details, credit of the pictures from [this StackOverflow post](https://unix.stackexchange.com/questions/115897/whats-ssh-port-forwarding-and-whats-the-difference-between-ssh-local-and-remot)). -->
此時就需要進行端口轉發(_port forwarding_)。端口轉發有兩種，一種是本地端口轉發和遠端伺服器發（參見下圖，該圖片引用自[這篇StackOverflow](https://unix.stackexchange.com/questions/115897/whats-ssh-port-forwarding-and-whats-the-difference-between-ssh-local-and-remot) 文章）中的圖片。

<!-- **Local Port Forwarding** -->
**本地端口轉發**
![Local Port Forwarding](https://i.stack.imgur.com/a28N8.png  "Local Port Forwarding")

<!-- **Remote Port Forwarding** -->
**遠端端口轉發**
![Remote Port Forwarding](https://i.stack.imgur.com/4iK3b.png  "Remote Port Forwarding")

<!-- The most common scenario is local port forwarding, where a service in the remote machine listens in a port and you want to link a port in your local machine to forward to the remote port. For example, if we execute  `jupyter notebook` in the remote server that listens to the port `8888`. Thus, to forward that to the local port `9999`, we would do `ssh -L 9999:localhost:8888 foobar@remote_server` and then navigate to `locahost:9999` in our local machine. -->
常見的情景是使用本地端口轉發，即遠端設備上的服務監聽一個端口，而您希望在本地設備上的一個端口建立連接並轉發到遠端伺服器。例如，我們在遠端伺服器上運行 `jupyter notebook` 並監聽 `8888` 端口。然後，建立從本地端口 `9999` 的轉發，使用 `ssh -L 9999:localhost:8888 foobar@remote_server` 。這樣只需要訪問本地的 `localhost:9999` 即可。


<!-- ## SSH Configuration -->
## SSH 配置

<!-- We have covered many many arguments that we can pass. A tempting alternative is to create shell aliases that look like -->
我們已經介紹了很多SSH可傳入的參數。為它們創建一個別名是個好主意，我們可以這樣做：
```bash
alias my_server="ssh -i ~/.id_ed25519 --port 2222 -L 9999:localhost:8888 foobar@remote_server
```

<!-- However, there is a better alternative using `~/.ssh/config`. -->
不過，更好的方法是使用 `~/.ssh/config`。

```bash
Host vm
    User foobar
    HostName 172.16.174.141
    Port 2222
    IdentityFile ~/.ssh/id_ed25519
    LocalForward 9999 localhost:8888

# 在配置文件中也可以使用萬用字元
Host *.mit.edu
    User foobaz
```

<!-- An additional advantage of using the `~/.ssh/config` file over aliases  is that other programs like `scp`, `rsync`, `mosh`, &c are able to read it as well and convert the settings into the corresponding flags. -->
這麼做的好處是，使用 `~/.ssh/config` 文件來創建別名，類似 `scp`、`rsync`和`mosh`等指令都可以讀取這個配置並將設置轉換為對應的命令列選項。

<!-- Note that the `~/.ssh/config` file can be considered a dotfile, and in general it is fine for it to be included with the rest of your dotfiles. However, if you make it public, think about the information that you are potentially providing strangers on the internet: addresses of your servers, users, open ports, &c. This may facilitate some types of attacks so be thoughtful about sharing your SSH configuration. -->
注意，`~/.ssh/config` 文件也可以被當作配置文件(dotfile)，而且一般情況下也是可以被引入到其他配置文件的。不過，如果您將其公開到網路上，那麼其他人都將會看到您的伺服器地址、用戶名、開放端口等等。這些資訊可能會幫助到那些企圖攻擊您系統的駭客所以請務必三思。

<!-- Server side configuration is usually specified in `/etc/ssh/sshd_config`. Here you can make changes like disabling password authentication, changing ssh ports, enabling X11 forwarding, &c. You can specify config settings on a per user basis. -->
伺服器的配置通常放在 `/etc/ssh/sshd_config`。您可以在這裡配置免密碼認證、修改 ssh 端口、開啟 X11 轉發等等。也可以為個別用戶指定配置。

<!-- ## Miscellaneous -->
## 雜項

<!-- A common pain when connecting to a remote server are disconnections due to shutting down/sleeping your computer or changing a network. Moreover if one has a connection with significant lag using ssh can become quite frustrating. [Mosh](https://mosh.org/), the mobile shell, improves upon ssh, allowing roaming connections, intermittent connectivity and providing intelligent local echo. -->
連接遠端伺服器的一個常見痛點是遇到由關機、休眠或網路環境變化導致的斷線。如果連接的延遲很高也很讓人討厭。 [Mosh](https://mosh.org/)（即 mobile shell ）對 ssh 進行了改進，它允許連接漫遊、間歇性連線及智慧本地回顯。 

<!-- Sometimes it is convenient to mount a remote folder. [sshfs](https://github.com/libfuse/sshfs) can mount a folder on a remote server
locally, and then you can use a local editor. -->
有時將一個遠端文件夾掛載到本地端會比較方便， [sshfs](https://github.com/libfuse/sshfs) 可以將遠端伺服器上的一個文件夾掛載到本地，然後您就可以使用本地的編輯器了。


<!-- # Shells & Frameworks -->
# Shell & 框架

<!-- During shell tool and scripting we covered the `bash` shell because it is by far the most ubiquitous shell and most systems have it as the default option. Nevertheless, it is not the only option. -->
在 shell 工具和腳本那節課中我們已經介紹了 `bash` shell，因為它是目前最通用的 shell，大多數的系統都將其作為默認 shell。但是，它並不是唯一的選項。

<!-- For example, the `zsh` shell is a superset of `bash` and provides many convenient features out of the box such as: -->
例如，`zsh` shell 是 `bash` 的父集合並提供了一些方便的功能：

<!-- - Smarter globbing, `**`
- Inline globbing/wildcard expansion
- Spelling correction
- Better tab completion/selection
- Path expansion (`cd /u/lo/b` will expand as `/usr/local/bin`) -->
- 智慧替換, ** 
- 行內替換/萬用字擴展 
- 拼寫修正 
- 更好的 tab 補全和選擇 
- 路徑展開 (`cd /u/lo/b` 會被展開為 `/usr/local/bin`)

<!-- **Frameworks** can improve your shell as well. Some popular general frameworks are [prezto](https://github.com/sorin-ionescu/prezto) or [oh-my-zsh](https://ohmyz.sh/), and smaller ones that focus on specific features such as [zsh-syntax-highlighting](https://github.com/zsh-users/zsh-syntax-highlighting) or [zsh-history-substring-search](https://github.com/zsh-users/zsh-history-substring-search). Shells like [fish](https://fishshell.com/) include many of these user-friendly features by default. Some of these features include: -->
**框架**也可以改進您的 shell。比較流行的通用框架包括 [prezto](https://github.com/sorin-ionescu/prezto) 或 [oh-my-zsh](https://ohmyz.sh/)。還有一些更精簡的框架，它們往往專注於某一個特定功能，例如 [zsh-syntax-highlighting](https://github.com/zsh-users/zsh-syntax-highlighting) 或 [zsh-history-substring-search](https://github.com/zsh-users/zsh-history-substring-search)。像 [fish](https://fishshell.com/) 這樣的 shell 包含了很多便利的功能，其中一些特性包括：

<!-- - Right prompt
- Command syntax highlighting
- History substring search
- manpage based flag completions
- Smarter autocompletion
- Prompt themes -->
- 向右對齊 
- 指令語法突顯 
- 歷史子字串查詢 
- 基於手冊頁面的選項補全 
- 更聰明的自動補全 
- 命令列介面主題

<!-- One thing to note when using these frameworks is that they may slow down your shell, especially if the code they run is not properly optimized or it is too much code. You can always profile it and disable the features that you do not use often or value over speed. -->
需要注意的是，使用這些框架可能會降低您 shell 的性能，尤其是如果這些框架的程式碼沒有最佳化或者過於冗長。您可以時常測試其性能或禁用某些不常用的功能來達到速度與功能的平衡。

<!-- # Terminal Emulators -->
# 終端模擬器 (Terminal Emulators)

<!-- Along with customizing your shell, it is worth spending some time figuring out your choice of **terminal emulator** and its settings. There are many many terminal emulators out there (here is a [comparison](https://anarc.at/blog/2018-04-12-terminal-emulators-1/)). -->
和自定義 shell 一樣，花點時間選擇適合您的**終端模擬器**並進行設置是很有必要的。有許多終端模擬器可供您選擇（這裡有一些關於它們之間[比較](https://anarc.at/blog/2018-04-12-terminal-emulators-1/)的資訊）。

<!-- Since you might be spending hundreds to thousands of hours in your terminal it pays off to look into its settings. Some of the aspects that you may want to modify in your terminal include: -->
您會花上很多時間在使用終端上，因此研究一下終端的設置是很有必要的，您可以從下面這些方面來設置您的終端：

<!-- - Font choice
- Color Scheme
- Keyboard shortcuts
- Tab/Pane support
- Scrollback configuration
- Performance (some newer terminals like [Alacritty](https://github.com/jwilm/alacritty) or [kitty](https://sw.kovidgoyal.net/kitty/) offer GPU acceleration). -->
- 字體選擇 
- 彩色主題 
- 快捷鍵 
- 分頁/面板支援 
- 回退設置 
- 性能（像 [Alacritty](https://github.com/jwilm/alacritty) 或者 [kitty](https://sw.kovidgoyal.net/kitty/) 這種比較新的終端，它們支持GPU加速）

<!-- # Exercises -->
# 課後練習

<!-- ## Job control -->
## 任務控制

<!-- 1. From what we have seen, we can use some `ps aux | grep` commands to get our jobs' pids and then kill them, but there are better ways to do it. Start a `sleep 10000` job in a terminal, background it with `Ctrl-Z` and continue its execution with `bg`. Now use [`pgrep`](https://www.man7.org/linux/man-pages/man1/pgrep.1.html) to find its pid and [`pkill`](http://man7.org/linux/man-pages/man1/pgrep.1.html) to kill it without ever typing the pid itself. (Hint: use the `-af` flags). -->
1. 我們可以使用類似 `ps aux | grep` 這樣的指令來獲取任務的 pid ，然後您可以根據 pid 來結束這些行程。但我們其實有更好的方法來做這件事。在終端中執行 `sleep 10000` 這個任務。然後用 `Ctrl-Z` 將其切換到後台並使用 `bg` 來繼續允許它。現在，使用 [`pgrep`](https://www.man7.org/linux/man-pages/man1/pgrep.1.html) 來查找 pid 並使用 [`pkill`](http://man7.org/linux/man-pages/man1/pgrep.1.html) 結束行程而不需要手動輸入pid。 (提示: 使用 `-af` 旗標)。

<!-- 1. Say you don't want to start a process until another completes, how you would go about it? In this exercise our limiting process will always be `sleep 60 &`.
One way to achieve this is to use the [`wait`](https://www.man7.org/linux/man-pages/man1/wait.1p.html) command. Try launching the sleep command and having an `ls` wait until the background process finishes.

    However, this strategy will fail if we start in a different bash session, since `wait` only works for child processes. One feature we did not discuss in the notes is that the `kill` command's exit status will be zero on success and nonzero otherwise. `kill -0` does not send a signal but will give a nonzero exit status if the process does not exist.
    Write a bash function called `pidwait` that takes a pid and waits until the given process completes. You should use `sleep` to avoid wasting CPU unnecessarily. -->
1. 如果您希望某個行程結束後再開始另外一個行程， 應該如何實現呢？在這個練習中，我們使用 `sleep 60 &` 作為先執行的程序。一種方法是使用 [`wait`](https://www.man7.org/linux/man-pages/man1/wait.1p.html) 指令。嘗試啟動這個休眠指令，然後待其結束後再執行 `ls` 指令。

    但是，如果我們在不同的 bash 會話中進行操作，則上述方法就不起作用了。因為 wait 只能對子行程起作用。之前我們沒有提過的一個特性是，`kill` 指令成功退出時其狀態碼為 0 ，其他狀態則是非0。 `kill -0` 則不會發送信號，但是會在行程不存在時返回一個不為0的狀態碼。請撰寫一個 bash 函數 `pidwait` ，它接受一個 pid 作為輸入參數，然後一直等待直到該行程結束。您需要使用 `sleep` 來避免浪費 CPU 性能。

<!-- ## Terminal multiplexer -->
## 終端多工器

<!-- 1. Follow this `tmux` [tutorial](https://www.hamvocke.com/blog/a-quick-and-easy-guide-to-tmux/) and then learn how to do some basic customizations following [these steps](https://www.hamvocke.com/blog/a-guide-to-customizing-your-tmux-conf/). -->
1. 請完成這個 `tmux` [教學](https://www.hamvocke.com/blog/a-quick-and-easy-guide-to-tmux/) 參考[這些步驟](https://www.hamvocke.com/blog/a-guide-to-customizing-your-tmux-conf/)來學習如何自定義 tmux。

<!-- ## Aliases -->
## 別名

<!-- 1. Create an alias `dc` that resolves to `cd` for when you type it wrongly. -->
1. 創建一個 `dc` 別名，它的功能是當我們錯誤的將 `cd` 輸入為 `dc` 時也能正確執行。

<!-- 1.  Run `history | awk '{$1="";print substr($0,2)}' | sort | uniq -c | sort -n | tail -n 10`  to get your top 10 most used commands and consider writing shorter aliases for them. Note: this works for Bash; if you're using ZSH, use `history 1` instead of just `history`. -->
1. 執行 `history | awk '{$1="";print substr($0,2)}' | sort | uniq -c | sort -n | tail -n 10` 來獲取您最常用的十條指令，嘗試為它們創建別名。注意：這個指令只在 Bash 中生效，如果您使用 ZSH，使用 `history 1` 替換 `history`。


<!-- ## Dotfiles -->
## 配置文件

<!-- Let's get you up to speed with dotfiles.
1. Create a folder for your dotfiles and set up version
   control.
1. Add a configuration for at least one program, e.g. your shell, with some customization (to start off, it can be something as simple as customizing your shell prompt by setting `$PS1`).
1. Set up a method to install your dotfiles quickly (and without manual effort) on a new machine. This can be as simple as a shell script that calls `ln -s` for each file, or you could use a [specialized utility](https://dotfiles.github.io/utilities/).
1. Test your installation script on a fresh virtual machine.
1. Migrate all of your current tool configurations to your dotfiles repository.
1. Publish your dotfiles on GitHub. -->

讓我們幫助您進一步學習自動化配置文件：

1. 為您的配置文件新建一個文件夾，並設置好版本控制 
1. 在其中添加至少一個配置文件，比如說您的 shell，在其中包含一些自定義設置（可以從設置 `$PS1` 開始）
1. 建立一種在新設備進行快速安裝配置的方法（無需手動操作）。最簡單的方法是寫一個 shell 腳本對每個文件使用 `ln -s`，也可以使用[專用工具](https://dotfiles.github.io/utilities/)
1. 在新的虛擬機上測試該安裝腳本
1. 將您現有的所有配置文件移動到dotfiles repository裡
1. 將項目發佈到GitHub。

<!-- ## Remote Machines -->
## 遠端連線

<!-- Install a Linux virtual machine (or use an already existing one) for this exercise. If you are not familiar with virtual machines check out [this](https://hibbard.eu/install-ubuntu-virtual-box/) tutorial for installing one. -->
進行下面的練習需要您先安裝一個 Linux 虛擬機（如果已經安裝過則可以直接使用），如果您對虛擬機尚不熟悉，可以參考[這篇](https://hibbard.eu/install-ubuntu-virtual-box/)教學來進行安裝。

<!-- 1. Go to `~/.ssh/` and check if you have a pair of SSH keys there. If not, generate them with `ssh-keygen -o -a 100 -t ed25519`. It is recommended that you use a password and use `ssh-agent` , more info [here](https://www.ssh.com/ssh/agent).
1. Edit `.ssh/config` to have an entry as follows -->
1. 前往 `~/.ssh/` 並查看是否已經存在 SSH 密鑰對。如果不存在，請使用`ssh-keygen -o -a 100 -t ed25519` 來創建一個。建議為密鑰設置密碼然後使用`ssh-agent`，更多信息可以參考[這裡](https://www.ssh.com/ssh/agent)
1. 在 `.ssh/config` 加入下面內容：

```bash
Host vm
    User username_goes_here
    HostName ip_goes_here
    IdentityFile ~/.ssh/id_ed25519
    LocalForward 9999 localhost:8888
```
<!-- 1. Use `ssh-copy-id vm` to copy your ssh key to the server.
1. Start a webserver in your VM by executing `python -m http.server 8888`. Access the VM webserver by navigating to `http://localhost:9999` in your machine.
1. Edit your SSH server config by doing  `sudo vim /etc/ssh/sshd_config` and disable password authentication by editing the value of `PasswordAuthentication`. Disable root login by editing the value of `PermitRootLogin`. Restart the `ssh` service with `sudo service sshd restart`. Try sshing in again.
1. (Challenge) Install [`mosh`](https://mosh.org/) in the VM and establish a connection. Then disconnect the network adapter of the server/VM. Can mosh properly recover from it?
1. (Challenge) Look into what the `-N` and `-f` flags do in `ssh` and figure out what a command to achieve background port forwarding. -->

1. 使用 `ssh-copy-id vm` 將您的 ssh 密鑰複製到伺服器。
1. 使用 `python -m http.server 8888` 在您的虛擬機中啟動一個 Web 伺服器並通過本機的 `http://localhost:9999` 訪問虛擬機上的 Web 伺服器 
1. 使用 `sudo vim /etc/ssh/sshd_config` 編輯 SSH 伺服器配置，通過修改`PasswordAuthentication` 的值來禁用密碼驗證。通過修改 `PermitRootLogin` 的值來禁用 root 登陸。然後使用 `sudo service sshd restart` 重啟 `ssh` 伺服器，然後重新嘗試。 
1. (進階題) 在虛擬機中安裝 [`mosh`](https://mosh.org/) 並啟動連接。然後斷開伺服器/虛擬機的網路卡。 mosh 可以恢復連接嗎？ 
1. (進階題) 查看 `ssh` 的 `-N` 和 `-f` 選項的作用，找出在後台進行端口轉發的命令是什麼？