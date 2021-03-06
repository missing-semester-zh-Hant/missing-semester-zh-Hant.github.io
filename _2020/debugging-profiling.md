---
layout: lecture
title: "除錯與分析 （尚未翻譯）"
date: 2019-01-23
ready: true
video:
  aspect: 56.25
  id: l812pUnKxME
---

<!-- A golden rule in programming is that code does not do what you expect it to do, but what you tell it to do.
Bridging that gap can sometimes be a quite difficult feat.
In this lecture we are going to cover useful techniques for dealing with buggy and resource hungry code: debugging and profiling. -->
程式設計中的一條準則是，程式碼不會直接給出您期望的結果，而是執行您指定的動作。
彌合這一差距有時是一項相當困難的。
在本講座中，我們將介紹處理錯誤和應對資源匱乏的程式碼的有用技術：除錯和效能分析。

<!-- # Debugging -->
# 偵錯

<!-- ## Printf debugging and Logging -->
## Printf 偵錯法與使用日誌

<!-- "The most effective debugging tool is still careful thought, coupled with judiciously placed print statements" — Brian Kernighan, _Unix for Beginners_. -->
"最有效的除錯工具就是認真思考，加上放置得當的列印語句" — Brian Kernighan, _Unix for Beginners_.

<!-- A first approach to debug a program is to add print statements around where you have detected the problem, and keep iterating until you have extracted enough information to understand what is responsible for the issue. -->
偵錯的第一次嘗試往往是在發現問題的地方新增一些列印語句，然後不斷重複直至你獲得了足夠的資訊並認識到了問題的原因。

<!-- A second approach is to use logging in your program, instead of ad hoc print statements. Logging is better than regular print statements for several reasons: -->
第二種方法是在程式中使用日誌記錄，而不是臨時印出語句。 由於以下幾個原因，使用日誌比常規的列印語句要好：

<!-- - You can log to files, sockets or even remote servers instead of standard output.
- Logging supports severity levels (such as INFO, DEBUG, WARN, ERROR, &c), that allow you to filter the output accordingly.
- For new issues, there's a fair chance that your logs will contain enough information to detect what is going wrong. -->
- 你可以將日誌寫入檔案，sockets 或者是伺服器而不僅僅是印出來。
- 日誌支援重要等級（例如 INFO, DEBUG, WARN, ERROR 等），允許你過濾日誌。
- 對於新問題，日誌常常已經包含了偵錯所需的足量訊息。

<!-- [Here](/static/files/logger.py) is an example code that logs messages: -->
[這裡](/static/files/logger.py) 有記錄日誌的示例程式碼:

```bash
$ python logger.py
# Raw output as with just prints
$ python logger.py log
# Log formatted output
$ python logger.py log ERROR
# Print only ERROR levels and above
$ python logger.py color
# Color formatted output
```
<!-- 
One of my favorite tips for making logs more readable is to color code them.
By now you probably have realized that your terminal uses colors to make things more readable. But how does it do it?
Programs like `ls` or `grep` are using [ANSI escape codes](https://en.wikipedia.org/wiki/ANSI_escape_code), which are special sequences of characters to indicate your shell to change the color of the output. For example, executing `echo -e "\e[38;2;255;0;0mThis is red\e[0m"` prints the message `This is red` in red on your terminal. The following script shows how to print many RGB colors into your terminal. -->
我最喜歡的加強日誌可讀性的技巧之一就是給它們塗上顏色。
現在你可能已經意識到在終端使用顏色可以使它更具可讀性。這是如何做到的？
像 `ls` 或 `grep` 這樣的程式正在使用 [ANSI 跳脫序列]（https://en.wikipedia.org/wiki/ANSI_escape_code），這種特殊字元序列用於指示你的 shell 更改輸出顏色。 例如，執行 `echo -e "\e[38;2;255;0;0mThis is red\e[0m"` 在終端上以紅色顯示 “ This is red”。 以下指令碼顯示瞭如何在終端中列印多種RGB顏色。

```bash
#!/usr/bin/env bash
for R in $(seq 0 20 255); do
    for G in $(seq 0 20 255); do
        for B in $(seq 0 20 255); do
            printf "\e[38;2;${R};${G};${B}m█\e[0m";
        done
    done
done
```

<!-- ## Third party logs -->
## 第三方日誌

<!-- As you start building larger software systems you will most probably run into dependencies that run as separate programs.
Web servers, databases or message brokers are common examples of this kind of dependencies.
When interacting with these systems it is often necessary to read their logs, since client side error messages might not suffice. -->
當你開始構建更大的程式系統時，很可能會遇到依賴項，這些依賴項可以作為單獨的程式執行。
Web 伺服器，資料庫或訊息代理是這種依賴關係的常見示例。
與這些系統進行互動時常常需要讀取其日誌，因為客戶端錯誤訊息可能不足。

<!-- Luckily, most programs write their own logs somewhere in your system.
In UNIX systems, it is commonplace for programs to write their logs under `/var/log`.
For instance, the [NGINX](https://www.nginx.com/) webserver places its logs under `/var/log/nginx`.
More recently, systems have started using a **system log**, which is increasingly where all of your log messages go.
Most (but not all) Linux systems use `systemd`, a system daemon that controls many things in your system such as which services are enabled and running.
`systemd` places the logs under `/var/log/journal` in a specialized format and you can use the [`journalctl`](https://www.man7.org/linux/man-pages/man1/journalctl.1.html) command to display the messages.
Similarly, on macOS there is still `/var/log/system.log` but an increasing number of tools use the system log, that can be displayed with [`log show`](https://www.manpagez.com/man/1/log/).
On most UNIX systems you can also use the [`dmesg`](https://www.man7.org/linux/man-pages/man1/dmesg.1.html) command to access the kernel log. -->
幸運地是，許多程式都會自己記錄日誌。
在 UNIX 系統中，程式通常會將日誌寫入 `/var/log`。
例如，[NGINX](https://www.nginx.com/) 伺服器會將日誌放入 `/var/log/nginx`。
現在的系統自身也會使用 **系統日誌**，這裡會記錄所有日誌訊息。
多數（但非全部） Linux 系統使用 `systemd`，它是一個可以控制系統內許多事情，例如有哪些服務被啟用的後臺駐留程式。
`systemd` 將日誌以特殊格式置於 `/var/log/journal`，你可以使用 [`journalctl`](https://www.man7.org/linux/man-pages/man1/journalctl.1.html) 來顯示它們。
在多數 UNIX 系統中你也可以使用 [`dmesg`](https://www.man7.org/linux/man-pages/man1/dmesg.1.html) 來存取核心日誌。

<!-- For logging under the system logs you can use the [`logger`](https://www.man7.org/linux/man-pages/man1/logger.1.html) shell program.
Here's an example of using `logger` and how to check that the entry made it to the system logs.
Moreover, most programming languages have bindings logging to the system log. -->
要在系統日誌下進行記錄，可以使用 [`logger`](https://www.man7.org/linux/man-pages/man1/logger.1.html) 這個 shell 程式。
下面是一個使用 `logger` 且檢查條目是否進入系統日誌的範例。
大多數程式語言都有將自身日誌記錄到系統日誌中的功能。

```bash
logger "Hello Logs"
# On macOS
log show --last 1m | grep Hello
# On Linux
journalctl --since "1m ago" | grep Hello
```

<!-- As we saw in the data wrangling lecture, logs can be quite verbose and they require some level of processing and filtering to get the information you want.
If you find yourself heavily filtering through `journalctl` and `log show` you can consider using their flags, which can perform a first pass of filtering of their output.
There are also some tools like  [`lnav`](http://lnav.org/), that provide an improved presentation and navigation for log files. -->
如同我們在資料處理這堂課上看到的， 日誌可能非常冗長，致使我們需要經過處理和過濾才能得到所需的訊息。
如果你經常使用 `journalctl` 和 `log show` 來處理日誌，你可以嘗試使用他們內建的 flag 進行初步過濾。
也有類似 [`lnav`](http://lnav.org/) 這樣的工具， 可以更優質地顯示日誌，以及更簡易地導航操作。

<!-- ## Debuggers -->
## 除錯工具（偵錯程式）

<!-- When printf debugging is not enough you should use a debugger.
Debuggers are programs that let you interact with the execution of a program, allowing the following: -->
我們應該使用偵錯程式來完成 printf 無法勝任的除錯工作。
偵錯程式是一種讓你可以和其他程式執行過程進行互動的程式，擁有以下能力：

<!-- - Halt execution of the program when it reaches a certain line.
- Step through the program one instruction at a time.
- Inspect values of variables after the program crashed.
- Conditionally halt the execution when a given condition is met.
- And many more advanced features -->
- 當程式執行到某一列時， 中斷程式執行
- 每次僅執行一條指令
- 程式當機後檢查所有變數
- 在滿足特定條件時中斷程式執行
- 更多進階功能

<!-- Many programming languages come with some form of debugger.
In Python this is the Python Debugger [`pdb`](https://docs.python.org/3/library/pdb.html). -->
許多程式語言都帶有偵錯程式。Python 使用 [`pdb`](https://docs.python.org/3/library/pdb.html) 作為其偵錯程式。

<!-- Here is a brief description of some of the commands `pdb` supports: -->
這裡是 `pdb` 支援的一部分指令的說明：

<!-- - **l**(ist) - Displays 11 lines around the current line or continue the previous listing.
- **s**(tep) - Execute the current line, stop at the first possible occasion.
- **n**(ext) - Continue execution until the next line in the current function is reached or it returns.
- **b**(reak) - Set a breakpoint (depending on the argument provided).
- **p**(rint) - Evaluate the expression in the current context and print its value. There's also **pp** to display using [`pprint`](https://docs.python.org/3/library/pprint.html) instead.
- **r**(eturn) - Continue execution until the current function returns.
- **q**(uit) - Quit the debugger. -->
- **l**(ist) - 顯示當前列上下的 11 列內容，或者繼續顯示上一次的 list 指令結果。
- **s**(tep) - 執行當前列，且在儘可能早地停止執行。
- **n**(ext) - 繼續執行，直至當前函式的下一列或遇到 return。
- **b**(reak) - （基於提供的引數）設定中斷點。
- **p**(rint) - 在當前上下文對算是求值並印出結果。有一個類似的指令是 **pp**， 它使用 [`pprint`](https://docs.python.org/3/library/pprint.html) 來顯示結果。
- **r**(eturn) -繼續執行，直至當前函式回傳。
- **q**(uit) - 結束偵錯程式。

<!-- Let's go through an example of using `pdb` to fix the following buggy python code. (See the lecture video). -->
讓我們看一個示例，使用 `pdb` 來修復錯誤的 Python 程式碼（請參考課程回放）

```python
def bubble_sort(arr):
    n = len(arr)
    for i in range(n):
        for j in range(n):
            if arr[j] > arr[j+1]:
                arr[j] = arr[j+1]
                arr[j+1] = arr[j]
    return arr

print(bubble_sort([4, 2, 1, 8, 7, 6]))
```


<!-- Note that since Python is an interpreted language we can use the `pdb` shell to execute commands and to execute instructions.
[`ipdb`](https://pypi.org/project/ipdb/) is an improved `pdb` that uses the [`IPython`](https://ipython.org) REPL enabling tab completion, syntax highlighting, better tracebacks, and better introspection while retaining the same interface as the `pdb` module. -->
Python 是一種直譯語言，所以我們可以透過 `pdb` shell 執行指令。
[`ipdb`](https://pypi.org/project/ipdb/) 是改進後的 `pdb`，
它使用 [`IPython`](https://ipython.org) 作為[REPL](https://en.wikipedia.org/wiki/Read%E2%80%93eval%E2%80%93print_loop) 並啟用了製表符補全，語法突出顯示，更優秀的回溯和內省功能，同時提供與 `pdb` 相同的介面。

<!-- For more low level programming you will probably want to look into [`gdb`](https://www.gnu.org/software/gdb/) (and its quality of life modification [`pwndbg`](https://github.com/pwndbg/pwndbg)) and [`lldb`](https://lldb.llvm.org/).
They are optimized for C-like language debugging but will let you probe pretty much any process and get its current machine state: registers, stack, program counter, &c. -->
對於更底層的程式語言，你可能會想要瞭解 [`gdb`](https://www.gnu.org/software/gdb/)（及它的易用版本 [`pwndbg`](https://github.com/pwndbg/pwndbg)）和 [`lldb`](https://lldb.llvm.org/)。他們都對類似 C 的語言進行了優化，使得我們可以探測幾乎所有行程與當前電腦狀態：包含暫存器，堆疊和程式計數器等。


<!-- ## Specialized Tools -->
## 專用工具

<!-- Even if what you are trying to debug is a black box binary there are tools that can help you with that.
Whenever programs need to perform actions that only the kernel can, they use [System Calls](https://en.wikipedia.org/wiki/System_call).
There are commands that let you trace the syscalls your program makes. In Linux there's [`strace`](https://www.man7.org/linux/man-pages/man1/strace.1.html) and macOS and BSD have [`dtrace`](http://dtrace.org/blogs/about/). `dtrace` can be tricky to use because it uses its own `D` language, but there is a wrapper called [`dtruss`](https://www.manpagez.com/man/1/dtruss/) that provides an interface more similar to `strace` (more details [here](https://8thlight.com/blog/colin-jones/2015/11/06/dtrace-even-better-than-strace-for-osx.html)). -->
即使我們要進行黑箱測試，仍然有一些工具可以使用。
當程式需要執行一些僅有系統內核可以執行的動作時，它們會使用[系統呼叫](https://en.wikipedia.org/wiki/System_call)。有一些指令可以讓我們追蹤程式使用的系統呼叫。在 linux 中，我們可以使用 [`strace`](https://www.man7.org/linux/man-pages/man1/strace.1.html)，在 macOS 與 BSD 裡則有 [`dtrace`](http://dtrace.org/blogs/about/)。dtrace 可能很難使用，因為它使用了自己的 `D` 語言，但是名為 [`dtruss`](https://www.manpagez.com/man/1/dtruss/) 的程式為其提供了類似 `strace` 的介面（更多細節見[這裡](https://8thlight.com/blog/colin-jones/2015/11/06/dtrace-even-better-than-strace-for-osx.html)）。

<!-- Below are some examples of using `strace` or `dtruss` to show [`stat`](https://www.man7.org/linux/man-pages/man2/stat.2.html) syscall traces for an execution of `ls`. For a deeper dive into `strace`, [this](https://blogs.oracle.com/linux/strace-the-sysadmins-microscope-v2) is a good read. -->
以下是一些使用 strace 或 dtruss 來展示 `ls` 指令執行時的 [`stat`](https://www.man7.org/linux/man-pages/man2/stat.2.html) 的示例。如果你想更深入瞭解 `strace`，可以閱讀[這裡](https://blogs.oracle.com/linux/strace-the-sysadmins-microscope-v2)。

```bash
# On Linux
sudo strace -e lstat ls -l > /dev/null
4
# On macOS
sudo dtruss -t lstat64_extended ls -l > /dev/null
```

<!-- Under some circumstances, you may need to look at the network packets to figure out the issue in your program.
Tools like [`tcpdump`](https://www.man7.org/linux/man-pages/man1/tcpdump.1.html) and [Wireshark](https://www.wireshark.org/) are network packet analyzers that let you read the contents of network packets and filter them based on different criteria. -->
在某些情況下，我們需要檢視網路封包來找出問題。我們可以使用類似於 [`tcpdump`](https://www.man7.org/linux/man-pages/man1/tcpdump.1.html) 和 [Wireshark](https://www.wireshark.org/) 這樣的網路封包分析器來讀取封包內容，並且根據所需過濾內容。

<!-- For web development, the Chrome/Firefox developer tools are quite handy. They feature a large number of tools, including:
- Source code - Inspect the HTML/CSS/JS source code of any website.
- Live HTML, CSS, JS modification - Change the website content, styles and behavior to test (you can see for yourself that website screenshots are not valid proofs).
- Javascript shell - Execute commands in the JS REPL.
- Network - Analyze the requests timeline.
- Storage - Look into the Cookies and local application storage. -->
對於 web 開發，Chrome 和 Firefox 的開發人員工具非常好用。它們可以實現諸如：
- 網頁原始碼 - 檢視任意頁面的 HTML/CSS/JS 原始碼
- 實時修改 HTML, CSS, JS - 修改頁面內容，樣式與行為來進行測試（所以網頁截圖不能作為推理證據）
- Javascript shell - 在 JS REPL 中執行指令
- Network - 分析網路請求時間線
- 儲存 - 檢視 Cookie 和本地程式儲存的內容

<!-- ## Static Analysis -->
## 靜態分析

<!-- For some issues you do not need to run any code.
For example, just by carefully looking at a piece of code you could realize that your loop variable is shadowing an already existing variable or function name; or that a program reads a variable before defining it.
Here is where [static analysis](https://en.wikipedia.org/wiki/Static_program_analysis) tools come into play.
Static analysis programs take source code as input and analyze it using coding rules to reason about its correctness. -->
對於一部分問題，無需執行程式碼即可解決。
例如，僅透過檢視一段程式碼，我們就可以意識到迴圈變數正在覆蓋一個已經存在的變數。或程式在宣告變數前先讀取變數。
這些就是 [靜態分析](https://en.wikipedia.org/wiki/Static_program_analysis) 工具大放異彩的地方。
靜態分析程式將原始碼作為輸入，透過檢視編碼規則來判斷程式碼是否正確。

<!-- In the following Python snippet there are several mistakes.
First, our loop variable `foo` shadows the previous definition of the function `foo`. We also wrote `baz` instead of `bar` in the last line, so the program will crash after completing the `sleep` call (which will take one minute). -->
在以下 Python 程式碼中存在一些錯誤。我們的迴圈變數 `foo` 覆蓋了函式 `foo` 的宣告，我們還在最後一列中寫入了 `baz`， 而非 `bar`，因此程式將在完成 `sleep` 後崩潰（需要大概一分鐘）。

```python
import time

def foo():
    return 42

for foo in range(5):
    print(foo)
bar = 1
bar *= 0.2
time.sleep(60)
print(baz)
```

<!-- Static analysis tools can identify this kind of issues. When we run [`pyflakes`](https://pypi.org/project/pyflakes) on the code we get the errors related to both bugs. [`mypy`](http://mypy-lang.org/) is another tool that can detect type checking issues. Here, `mypy` will warn us that `bar` is initially an `int` and is then casted to a `float`.
Again, note that all these issues were detected without having to run the code. -->
靜態分析工具可以認出這些問題。 當我們執行 [`pyflakes`](https://pypi.org/project/pyflakes) 時候，它會提示與此兩個 bug 相關的錯誤。 [`mypy`](http://mypy-lang.org/) 是一個可以檢測型別的工具。在這裡，`mypy` 會提示 `bar` 之前是一個 `int`，之後被強制轉換成了 `float`。
注意我們無需執行程式碼就可以檢測這類問題。

<!-- In the shell tools lecture we covered [`shellcheck`](https://www.shellcheck.net/), which is a similar tool for shell scripts. -->
在 shell 講座中，我們介紹了 [`shellcheck`](https://www.shellcheck.net/)，這是檢測 shell 指令碼的工具。

```bash
$ pyflakes foobar.py
foobar.py:6: redefinition of unused 'foo' from line 3
foobar.py:11: undefined name 'baz'

$ mypy foobar.py
foobar.py:6: error: Incompatible types in assignment (expression has type "int", variable has type "Callable[[], Any]")
foobar.py:9: error: Incompatible types in assignment (expression has type "float", variable has type "int")
foobar.py:11: error: Name 'baz' is not defined
Found 3 errors in 1 file (checked 1 source file)
```

<!-- Most editors and IDEs support displaying the output of these tools within the editor itself, highlighting the locations of warnings and errors.
This is often called **code linting** and it can also be used to display other types of issues such as stylistic violations or insecure constructs. -->
多數編輯器和 IDE 通常都支援這類程式直接將輸出顯示在程式碼編輯頁面上，來凸顯警告和錯誤提醒。
我們將其叫做 **code linting**，它也常常被用來顯示其他種類的問題，例如風格違規或不安全的建構。

<!-- In vim, the plugins [`ale`](https://vimawesome.com/plugin/ale) or [`syntastic`](https://vimawesome.com/plugin/syntastic) will let you do that.
For Python, [`pylint`](https://github.com/PyCQA/pylint) and [`pep8`](https://pypi.org/project/pep8/) are examples of stylistic linters and [`bandit`](https://pypi.org/project/bandit/) is a tool designed to find common security issues.
For other languages people have compiled comprehensive lists of useful static analysis tools, such as [Awesome Static Analysis](https://github.com/mre/awesome-static-analysis) (you may want to take a look at the _Writing_ section) and for linters there is [Awesome Linters](https://github.com/caramelomartins/awesome-linters). -->
在 Vim 中，可以使用外掛 [`ale`](https://vimawesome.com/plugin/ale) 或者 [`syntastic`](https://vimawesome.com/plugin/syntastic)。
對於 Python, 使用[`pylint`](https://github.com/PyCQA/pylint) 和 [`pep8`](https://pypi.org/project/pep8/) 檢查編碼風格，使用 [`bandit`](https://pypi.org/project/bandit/) 檢查常見安全問題。
人們同樣為其他語言編譯了許多有用的靜態分析工具，例如 [Awesome Static Analysis](https://github.com/mre/awesome-static-analysis) (如果想瞭解更多，檢視 _Writing_ 部分)，而 [Awesome Linters](https://github.com/caramelomartins/awesome-linters) 提供了 linting 功能.

<!-- A complementary tool to stylistic linting are code formatters such as [`black`](https://github.com/psf/black) for Python, `gofmt` for Go, `rustfmt` for Rust or [`prettier`](https://prettier.io/) for JavaScript, HTML and CSS.
These tools autoformat your code so that it's consistent with common stylistic patterns for the given programming language.
Although you might be unwilling to give stylistic control about your code, standardizing code format will help other people read your code and will make you better at reading other people's (stylistically standardized) code. -->
我們也可以使用程式碼格式化工具作為補充功能。例如 Python 中的 [`black`](https://github.com/psf/black)，Go 中的 `gofmt`，適用於 Rust 的 `rustfmt`，以及可用於 JS/HTML/CSS 的 [`prettier`](https://prettier.io/)。
這些工具可以自動格式化程式碼，讓它們與這些程式語言的常見樣式保持一致。
儘管我們有時候不願意更改程式碼樣式，但是標準化的格式有助於他人閱讀我們的程式，也讓我們更容易閱讀其他人寫出的（標準化）程式。

<!-- # Profiling -->
# 分析

<!-- Even if your code functionally behaves as you would expect, that might not be good enough if it takes all your CPU or memory in the process.
Algorithms classes often teach big _O_ notation but not how to find hot spots in your programs.
Since [premature optimization is the root of all evil](http://wiki.c2.com/?PrematureOptimization), you should learn about profilers and monitoring tools. They will help you understand which parts of your program are taking most of the time and/or resources so you can focus on optimizing those parts. -->
雖然你的程式可以正常執行，如果它佔據了過多的 CPU 或 記憶體資源，那它仍然不夠好。演算法課程通常講授大 _O_ 表示法，但往往不會教你如何在程式中找到哪部分在耗費資源。
[過早最佳化是萬惡的根源](http://wiki.c2.com/?PrematureOptimization), 我們應該瞭解探查器和檢視工具，來檢測是哪些部分佔用了大部分時間和/或資源，讓我們可以專注優化這些部分。

<!-- ## Timing -->
## 計時

<!-- Similarly to the debugging case, in many scenarios it can be enough to just print the time it took your code between two points.
Here is an example in Python using the [`time`](https://docs.python.org/3/library/time.html) module. -->
與我們做除錯的情況類似，許多時候僅印出兩點之間的程式嗎使用了多少時長就夠了。
這是 Python 中的 [`time`](https://docs.python.org/3/library/time.html) 模組的示例。

```python
import time, random
n = random.randint(1, 10) * 100

# Get current time
start = time.time()

# Do some work
print("Sleeping for {} ms".format(n))
time.sleep(n/1000)

# Compute time between start and now
print(time.time() - start)

# Output
# Sleeping for 500 ms
# 0.5713930130004883
```

However, wall clock time can be misleading since your computer might be running other processes at the same time or waiting for events to happen. It is common for tools to make a distinction between _Real_, _User_ and _Sys_ time. In general, _User_ + _Sys_ tells you how much time your process actually spent in the CPU (more detailed explanation [here](https://stackoverflow.com/questions/556405/what-do-real-user-and-sys-mean-in-the-output-of-time1)).

- _Real_ - Wall clock elapsed time from start to finish of the program, including the time taken by other processes and time taken while blocked (e.g. waiting for I/O or network)
- _User_ - Amount of time spent in the CPU running user code
- _Sys_ - Amount of time spent in the CPU running kernel code

For example, try running a command that performs an HTTP request and prefixing it with [`time`](https://www.man7.org/linux/man-pages/man1/time.1.html). Under a slow connection you might get an output like the one below. Here it took over 2 seconds for the request to complete but the process only took 15ms of CPU user time and 12ms of kernel CPU time.

```bash
$ time curl https://missing.csail.mit.edu &> /dev/null`
real    0m2.561s
user    0m0.015s
sys     0m0.012s
```

## Profilers

### CPU

Most of the time when people refer to _profilers_ they actually mean _CPU profilers_,  which are the most common.
There are two main types of CPU profilers: _tracing_ and _sampling_ profilers.
Tracing profilers keep a record of every function call your program makes whereas sampling profilers probe your program periodically (commonly every millisecond) and record the program's stack.
They use these records to present aggregate statistics of what your program spent the most time doing.
[Here](https://jvns.ca/blog/2017/12/17/how-do-ruby---python-profilers-work-) is a good intro article if you want more detail on this topic.

Most programming languages have some sort of command line profiler that you can use to analyze your code.
They often integrate with full fledged IDEs but for this lecture we are going to focus on the command line tools themselves.

In Python we can use the `cProfile` module to profile time per function call. Here is a simple example that implements a rudimentary grep in Python:

```python
#!/usr/bin/env python

import sys, re

def grep(pattern, file):
    with open(file, 'r') as f:
        print(file)
        for i, line in enumerate(f.readlines()):
            pattern = re.compile(pattern)
            match = pattern.search(line)
            if match is not None:
                print("{}: {}".format(i, line), end="")

if __name__ == '__main__':
    times = int(sys.argv[1])
    pattern = sys.argv[2]
    for i in range(times):
        for file in sys.argv[3:]:
            grep(pattern, file)
```

We can profile this code using the following command. Analyzing the output we can see that IO is taking most of the time and that compiling the regex takes a fair amount of time as well. Since the regex only needs to be compiled once, we can factor it out of the for.

```
$ python -m cProfile -s tottime grep.py 1000 '^(import|\s*def)[^,]*$' *.py

[omitted program output]

 ncalls  tottime  percall  cumtime  percall filename:lineno(function)
     8000    0.266    0.000    0.292    0.000 {built-in method io.open}
     8000    0.153    0.000    0.894    0.000 grep.py:5(grep)
    17000    0.101    0.000    0.101    0.000 {built-in method builtins.print}
     8000    0.100    0.000    0.129    0.000 {method 'readlines' of '_io._IOBase' objects}
    93000    0.097    0.000    0.111    0.000 re.py:286(_compile)
    93000    0.069    0.000    0.069    0.000 {method 'search' of '_sre.SRE_Pattern' objects}
    93000    0.030    0.000    0.141    0.000 re.py:231(compile)
    17000    0.019    0.000    0.029    0.000 codecs.py:318(decode)
        1    0.017    0.017    0.911    0.911 grep.py:3(<module>)

[omitted lines]
```


A caveat of Python's `cProfile` profiler (and many profilers for that matter) is that they display time per function call. That can become unintuitive really fast, specially if you are using third party libraries in your code since internal function calls are also accounted for.
A more intuitive way of displaying profiling information is to include the time taken per line of code, which is what _line profilers_ do.

For instance, the following piece of Python code performs a request to the class website and parses the response to get all URLs in the page:

```python
#!/usr/bin/env python
import requests
from bs4 import BeautifulSoup

# This is a decorator that tells line_profiler
# that we want to analyze this function
@profile
def get_urls():
    response = requests.get('https://missing.csail.mit.edu')
    s = BeautifulSoup(response.content, 'lxml')
    urls = []
    for url in s.find_all('a'):
        urls.append(url['href'])

if __name__ == '__main__':
    get_urls()
```

If we used Python's `cProfile` profiler we'd get over 2500 lines of output, and even with sorting it'd be hard to understand where the time is being spent. A quick run with [`line_profiler`](https://github.com/rkern/line_profiler) shows the time taken per line:

```bash
$ kernprof -l -v a.py
Wrote profile results to urls.py.lprof
Timer unit: 1e-06 s

Total time: 0.636188 s
File: a.py
Function: get_urls at line 5

Line #  Hits         Time  Per Hit   % Time  Line Contents
==============================================================
 5                                           @profile
 6                                           def get_urls():
 7         1     613909.0 613909.0     96.5      response = requests.get('https://missing.csail.mit.edu')
 8         1      21559.0  21559.0      3.4      s = BeautifulSoup(response.content, 'lxml')
 9         1          2.0      2.0      0.0      urls = []
10        25        685.0     27.4      0.1      for url in s.find_all('a'):
11        24         33.0      1.4      0.0          urls.append(url['href'])
```

### Memory

In languages like C or C++ memory leaks can cause your program to never release memory that it doesn't need anymore.
To help in the process of memory debugging you can use tools like [Valgrind](https://valgrind.org/) that will help you identify memory leaks.

In garbage collected languages like Python it is still useful to use a memory profiler because as long as you have pointers to objects in memory they won't be garbage collected.
Here's an example program and its associated output when running it with [memory-profiler](https://pypi.org/project/memory-profiler/) (note the decorator like in `line-profiler`).

```python
@profile
def my_func():
    a = [1] * (10 ** 6)
    b = [2] * (2 * 10 ** 7)
    del b
    return a

if __name__ == '__main__':
    my_func()
```

```bash
$ python -m memory_profiler example.py
Line #    Mem usage  Increment   Line Contents
==============================================
     3                           @profile
     4      5.97 MB    0.00 MB   def my_func():
     5     13.61 MB    7.64 MB       a = [1] * (10 ** 6)
     6    166.20 MB  152.59 MB       b = [2] * (2 * 10 ** 7)
     7     13.61 MB -152.59 MB       del b
     8     13.61 MB    0.00 MB       return a
```

### Event Profiling

As it was the case for `strace` for debugging, you might want to ignore the specifics of the code that you are running and treat it like a black box when profiling.
The [`perf`](https://www.man7.org/linux/man-pages/man1/perf.1.html) command abstracts CPU differences away and does not report time or memory, but instead it reports system events related to your programs.
For example, `perf` can easily report poor cache locality, high amounts of page faults or livelocks. Here is an overview of the command:

- `perf list` - List the events that can be traced with perf
- `perf stat COMMAND ARG1 ARG2` - Gets counts of different events related a process or command
- `perf record COMMAND ARG1 ARG2` - Records the run of a command and saves the statistical data into a file called `perf.data`
- `perf report` - Formats and prints the data collected in `perf.data`


### Visualization

Profiler output for real world programs will contain large amounts of information because of the inherent complexity of software projects.
Humans are visual creatures and are quite terrible at reading large amounts of numbers and making sense of them.
Thus there are many tools for displaying profiler's output in an easier to parse way.

One common way to display CPU profiling information for sampling profilers is to use a [Flame Graph](http://www.brendangregg.com/flamegraphs.html), which will display a hierarchy of function calls across the Y axis and time taken proportional to the X axis. They are also interactive, letting you zoom into specific parts of the program and get their stack traces (try clicking in the image below).

[![FlameGraph](http://www.brendangregg.com/FlameGraphs/cpu-bash-flamegraph.svg)](http://www.brendangregg.com/FlameGraphs/cpu-bash-flamegraph.svg)

Call graphs or control flow graphs display the relationships between subroutines within a program by including functions as nodes and functions calls between them as directed edges. When coupled with profiling information such as the number of calls and time taken, call graphs can be quite useful for interpreting the flow of a program.
In Python you can use the [`pycallgraph`](http://pycallgraph.slowchop.com/en/master/) library to generate them.

![Call Graph](https://upload.wikimedia.org/wikipedia/commons/2/2f/A_Call_Graph_generated_by_pycallgraph.png)


## Resource Monitoring

Sometimes, the first step towards analyzing the performance of your program is to understand what its actual resource consumption is.
Programs often run slowly when they are resource constrained, e.g. without enough memory or on a slow network connection.
There are a myriad of command line tools for probing and displaying different system resources like CPU usage, memory usage, network, disk usage and so on.

- **General Monitoring** - Probably the most popular is [`htop`](https://hisham.hm/htop/index.php), which is an improved version of [`top`](https://www.man7.org/linux/man-pages/man1/top.1.html).
`htop` presents various statistics for the currently running processes on the system. `htop` has a myriad of options and keybinds, some useful ones  are: `<F6>` to sort processes, `t` to show tree hierarchy and `h` to toggle threads. 
See also [`glances`](https://nicolargo.github.io/glances/) for similar implementation with a great UI. For getting aggregate measures across all processes, [`dstat`](http://dag.wiee.rs/home-made/dstat/) is another nifty tool that computes real-time resource metrics for lots of different subsystems like I/O, networking, CPU utilization, context switches, &c.
- **I/O operations** - [`iotop`](https://www.man7.org/linux/man-pages/man8/iotop.8.html) displays live I/O usage information and is handy to check if a process is doing heavy I/O disk operations
- **Disk Usage** - [`df`](https://www.man7.org/linux/man-pages/man1/df.1.html) displays metrics per partitions and [`du`](http://man7.org/linux/man-pages/man1/du.1.html) displays **d**isk **u**sage per file for the current directory. In these tools the `-h` flag tells the program to print with **h**uman readable format.
A more interactive version of `du` is [`ncdu`](https://dev.yorhel.nl/ncdu) which lets you navigate folders and delete files and folders as you navigate.
- **Memory Usage** - [`free`](https://www.man7.org/linux/man-pages/man1/free.1.html) displays the total amount of free and used memory in the system. Memory is also displayed in tools like `htop`.
- **Open Files** - [`lsof`](https://www.man7.org/linux/man-pages/man8/lsof.8.html)  lists file information about files opened by processes. It can be quite useful for checking which process has opened a specific file.
- **Network Connections and Config** - [`ss`](https://www.man7.org/linux/man-pages/man8/ss.8.html) lets you monitor incoming and outgoing network packets statistics as well as interface statistics. A common use case of `ss` is figuring out what process is using a given port in a machine. For displaying routing, network devices and interfaces you can use [`ip`](http://man7.org/linux/man-pages/man8/ip.8.html). Note that `netstat` and `ifconfig` have been deprecated in favor of the former tools respectively.
- **Network Usage** -  [`nethogs`](https://github.com/raboof/nethogs) and [`iftop`](http://www.ex-parrot.com/pdw/iftop/) are good interactive CLI tools for monitoring network usage.

If you want to test these tools you can also artificially impose loads on the machine using the [`stress`](https://linux.die.net/man/1/stress) command.


### Specialized tools

Sometimes, black box benchmarking is all you need to determine what software to use.
Tools like [`hyperfine`](https://github.com/sharkdp/hyperfine) let you quickly benchmark command line programs.
For instance, in the shell tools and scripting lecture we recommended `fd` over `find`. We can use `hyperfine` to compare them in tasks we run often.
E.g. in the example below `fd` was 20x faster than `find` in my machine.

```bash
$ hyperfine --warmup 3 'fd -e jpg' 'find . -iname "*.jpg"'
Benchmark #1: fd -e jpg
  Time (mean ± σ):      51.4 ms ±   2.9 ms    [User: 121.0 ms, System: 160.5 ms]
  Range (min … max):    44.2 ms …  60.1 ms    56 runs

Benchmark #2: find . -iname "*.jpg"
  Time (mean ± σ):      1.126 s ±  0.101 s    [User: 141.1 ms, System: 956.1 ms]
  Range (min … max):    0.975 s …  1.287 s    10 runs

Summary
  'fd -e jpg' ran
   21.89 ± 2.33 times faster than 'find . -iname "*.jpg"'
```

As it was the case for debugging, browsers also come with a fantastic set of tools for profiling webpage loading, letting you figure out where time is being spent (loading, rendering, scripting, &c).
More info for [Firefox](https://developer.mozilla.org/en-US/docs/Mozilla/Performance/Profiling_with_the_Built-in_Profiler) and [Chrome](https://developers.google.com/web/tools/chrome-devtools/rendering-tools).

# Exercises

## Debugging
1. Use `journalctl` on Linux or `log show` on macOS to get the super user accesses and commands in the last day.
If there aren't any you can execute some harmless commands such as `sudo ls` and check again.

1. Do [this](https://github.com/spiside/pdb-tutorial) hands on `pdb` tutorial to familiarize yourself with the commands. For a more in depth tutorial read [this](https://realpython.com/python-debugging-pdb).

1. Install [`shellcheck`](https://www.shellcheck.net/) and try checking the following script. What is wrong with the code? Fix it. Install a linter plugin in your editor so you can get your warnings automatically.

   ```bash
   #!/bin/sh
   ## Example: a typical script with several problems
   for f in $(ls *.m3u)
   do
     grep -qi hq.*mp3 $f \
       && echo -e 'Playlist $f contains a HQ file in mp3 format'
   done
   ```

1. (Advanced) Read about [reversible debugging](https://undo.io/resources/reverse-debugging-whitepaper/) and get a simple example working using [`rr`](https://rr-project.org/) or [`RevPDB`](https://morepypy.blogspot.com/2016/07/reverse-debugging-for-python.html).
## Profiling

1. [Here](/static/files/sorts.py) are some sorting algorithm implementations. Use [`cProfile`](https://docs.python.org/3/library/profile.html) and [`line_profiler`](https://github.com/rkern/line_profiler) to compare the runtime of insertion sort and quicksort. What is the bottleneck of each algorithm? Use then `memory_profiler` to check the memory consumption, why is insertion sort better? Check now the inplace version of quicksort. Challenge: Use `perf` to look at the cycle counts and cache hits and misses of each algorithm.

1. Here's some (arguably convoluted) Python code for computing Fibonacci numbers using a function for each number.

   ```python
   #!/usr/bin/env python
   def fib0(): return 0

   def fib1(): return 1

   s = """def fib{}(): return fib{}() + fib{}()"""

   if __name__ == '__main__':

       for n in range(2, 10):
           exec(s.format(n, n-1, n-2))
       # from functools import lru_cache
       # for n in range(10):
       #     exec("fib{} = lru_cache(1)(fib{})".format(n, n))
       print(eval("fib9()"))
   ```

   Put the code into a file and make it executable. Install [`pycallgraph`](http://pycallgraph.slowchop.com/en/master/). Run the code as is with `pycallgraph graphviz -- ./fib.py` and check the `pycallgraph.png` file. How many times is `fib0` called?. We can do better than that by memoizing the functions. Uncomment the commented lines and regenerate the images. How many times are we calling each `fibN` function now?

1. A common issue is that a port you want to listen on is already taken by another process. Let's learn how to discover that process pid. First execute `python -m http.server 4444` to start a minimal web server listening on port `4444`. On a separate terminal run `lsof | grep LISTEN` to print all listening processes and ports. Find that process pid and terminate it by running `kill <PID>`.

1. Limiting processes resources can be another handy tool in your toolbox.
Try running `stress -c 3` and visualize the CPU consumption with `htop`. Now, execute `taskset --cpu-list 0,2 stress -c 3` and visualize it. Is `stress` taking three CPUs? Why not? Read [`man taskset`](https://www.man7.org/linux/man-pages/man1/taskset.1.html).
Challenge: achieve the same using [`cgroups`](https://www.man7.org/linux/man-pages/man7/cgroups.7.html). Try limiting the memory consumption of `stress -m`.

1. (Advanced) The command `curl ipinfo.io` performs a HTTP request an fetches information about your public IP. Open [Wireshark](https://www.wireshark.org/) and try to sniff the request and reply packets that `curl` sent and received. (Hint: Use the `http` filter to just watch HTTP packets).

