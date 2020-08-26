---
layout: lecture
title: "Q&A （尚未翻譯）"
date: 2019-01-30
ready: true
video:
  aspect: 56.25
  id: Wz50FvGG6xU
---

<!-- For the last lecture, we answered questions that the students submitted: -->
在最後一次課上，我們回答了一些學生提出的問題：

- [關於學習作業系統相關題目的建議，如進程，虛擬內存，中斷，內存管理](#關於學習作業系統相關題目的建議如進程虛擬內存中斷內存管理)
- [最適宜早先學習的工具有哪些?](#最適宜早先學習的工具有哪些)
- [When do I use Python versus a Bash scripts versus some other language?](#我該使用-Python-還是-Bash-腳本還是其他語言)
- [`source script.sh` 和 `./script.sh` 有什麼區別](#source-scriptsh-和-scriptsh-有什麼區別)
- [各種程式套件和工具的儲存位置是什麼？如何引用它們？ `/bin` 和 `/lib` 又是什麼？](#各種程式套件和工具的儲存位置是什麼如何引用它們-bin-和-lib-又是什麼？)
- [我是否可以 `apt-get install` 一個 python 程式, 或者使用 `pip install`?](#我是否可以-apt-get-install-一個-python-程式或者使用-pip-install)
- [提升程式碼性能最簡單好用的分析工具有哪些](#提升程式碼性能最簡單好用的分析工具有哪些)
- [你在用什麼瀏覽器外掛](#你在用什麼瀏覽器外掛)
- [還有哪些有用的資料預處理工具](#還有哪些有用的資料預處理工具)
- [Docker 和虛擬機有什麼區別](#Docker-和虛擬機有什麼區別)
- [每個作業系統的優缺點是什麼，我們如何在它們之間進行選擇（例如，為我們的目的選擇最適用的Linux發行版）？](#每個作業系統的優缺點是什麼我們如何在它們之間進行選擇例如為我們的目的選擇最適用的Linux發行版)
- [Vim 還是 Emacs](#Vim-還是-Emacs)
- [有哪些機器學習的注意與技巧](#有哪些機器學習的注意與技巧)
- [還有什麼 Vim 小技巧](#還有什麼-Vim-小技巧)
- [雙重驗證（2FA）是什麼，我該如何使用它](#雙重驗證2FA是什麼我該如何使用它)
- [對Web瀏覽器之間的差異有何評論](#對Web瀏覽器之間的差異有何評論)

<!-- ## Any recommendations on learning Operating Systems related topics like processes, virtual memory, interrupts, memory management, etc  -->
## 關於學習作業系統相關題目的建議，如進程，虛擬內存，中斷，內存管理

<!-- First, it is unclear whether you actually need to be very familiar with all of these topics since they are very low level topics.
They will matter as you start writing more low level code like implementing or modifying a kernel. Otherwise, most topics will not be relevant, with the exception of processes and signals that were briefly covered in other lectures.  -->
首先，我們不確定你是否真的需要非常熟悉這些，因爲他們是非常底層的題目。
如果你要開始編寫底層程式碼，如實現或者修改內核時，它們會比較重要，否則除了課上簡單瞭解的那些之外，這往往不相干。

<!-- Some good resources to learn about this topic: -->
關於這些的學習資源：

<!-- - [MIT's 6.828 class](https://pdos.csail.mit.edu/6.828/) - Graduate level class on Operating System Engineering. Class materials are publicly available.
- Modern Operating Systems (4th ed) - by Andrew S. Tanenbaum is a good overview of many of the mentioned concepts.
- The Design and Implementation of the FreeBSD Operating System - A good resource about the FreeBSD OS (note that this is not Linux). 
- Other guides like [Writing an OS in Rust](https://os.phil-opp.com/) where people implement a kernel step by step in various languages, mostly for teaching purposes.  -->
- [MIT's 6.828 class](https://pdos.csail.mit.edu/6.828/) - 碩士級別的作業系統課程。課程是公開的。
- 由 Andrew S. Tanenbaum 寫就的 Modern Operating Systems (4th ed) 良好地概述了上面的概念。
- The Design and Implementation of the FreeBSD Operating System - 關於 Free BSD 的良好資源（注意不是Linux）。
- 其他類似於 [Writing an OS in Rust](https://os.phil-opp.com/) 的指南，人們用各種語言逐步實現內核，一般出於教學目的。


<!-- ## What are some of the tools you'd prioritize learning first? -->
## 最適宜早先學習的工具有哪些

<!-- Some topics worth prioritizing: -->
一些適合學習的內容：

<!-- - Learning how to use your keyboard more and your mouse less. This can be through keyboard shortcuts, changing interfaces, &c.
- Learning your editor well. As a programmer most of your time is spent editing files so it really pays off to learn this skill well.
- Learning how to automate and/or simplify repetitive tasks in your workflow because the time savings will be enormous...
- Learning about version control tools like Git and how to use it in conjunction with GitHub to collaborate in modern software projects. -->
- 如何多使用鍵盤，少使用滑鼠。可以嘗試多加利用快捷鍵，改變介面等方法實現。
- 學好編輯器。你大部分時間都在編輯文件，非常值得去學習這些能力。
- 學習如何自動化或簡化重複性工作，這將節約大量時間。
- 學習 Git 等版本控制工具，以及如何將它們與 Github 結合，以便在現代軟件項目中協同工作。

<!-- ## When do I use Python versus a Bash scripts versus some other language? -->
## 我該使用 Python 還是 Bash 腳本還是其他語言

<!-- In general, bash scripts are useful for short and simple one-off scripts when you just want to run a specific series of commands. bash has a set of oddities that make it hard to work with for larger programs or scripts: -->
大部分時候，bash 腳本適宜一次性的簡短工作，比如你想要運行一系列指令的時候。bash 腳本有一些奇怪的地方，讓大型程序和腳本難以用 bash 實現。

<!-- - bash is easy to get right for a simple use case but it can be really hard to get right for all possible inputs. For example, spaces in script arguments have led to countless bugs in bash scripts.
- bash is not amenable to code reuse so it can be hard to reuse components of previous programs you have written. More generally, there is no concept of software libraries in bash.
- bash relies on many magic strings like `$?` or `$@` to refer to specific values, whereas other languages refer to them explicitly, like `exitCode` or `sys.args` respectively.  -->
- bash 可以獲取簡單的使用範例，但是很難獲得全部可能的輸入。例如腳本參數中的空格會導致其出錯
- bash 不適合程式碼重複使用，因此你以前編寫的程式祖堅很難被重用。而且，Bash 沒有庫這種概念。
- bash 依賴例如 `$?` 或者 `$@` 這些奇妙字串來指代指定值，而其他語言則顯式地引用它們，例如使用`exitCode`或`sys.args`。

<!-- Therefore, for larger and/or more complex scripts we recommend using more mature scripting languages like Python or Ruby. 
You can find online countless libraries that people have already written to solve common problems in these languages.
If you find a library that implements the specific functionality you care about in some language, usually the best thing to do is to just use that language. -->
因此，對於大型和/或更複雜的腳本，我們建議使用更成熟的腳本語言，比如 Python 或者 Ruby。
你可以找到人們爲了解決這些語言的常見問題而編寫的無數在線資料。
如果在某種語言裏你找到了實現某種特定需求的庫，通常最好的辦法就是使用這種語言。

<!-- ## What is the difference between `source script.sh` and `./script.sh` -->
## `source script.sh` 和 `./script.sh` 有什麼區別

<!-- In both cases the `script.sh` will be read and executed in a bash session, the difference lies in which session is running the commands.
For `source` the commands are executed in your current bash session and thus any changes made to the current environment, like changing directories or defining functions will persist in the current session once the `source` command finishes executing.
When running the script standalone like `./script.sh`, your current bash session starts a new instance of bash that will run the commands in `script.sh`.
Thus, if `script.sh` changes directories, the new bash instance will change directories but once it exits and returns control to the parent bash session, the parent session will remain in the same place.
Similarly, if `script.sh` defines a function that you want to access in your terminal, you need to `source` it for it to be defined in your current bash session. Otherwise, if you run it, the new bash process will be the one to process the function definition instead of your current shell. -->
在兩種情況下，在 bash 會話中都會讀取並執行`script.sh`，不同之處是哪個會話在執行指令。
對於 `source` 指令是在當前的bash會話中執行的，因此在其執行完畢後，對當前環境的任何更改，比如更改路徑或定義函數，都會在當前會話中保留。
當如同  `./script.sh` 這樣單獨執行腳本時候，當前的 bash 會話會啓動一個新的 bash 實例，然後這個實例會執行  `script.sh` 中的指令。
因此，如果 `script.sh` 更改了路徑，新的 bash 實例會更改，不過一單退出並且將控制權返回給父 bash 會話，父會話會保留在同一位置。
同樣，如果  `./script.sh` 定義了要在終端中訪問的功能，要對其執行 `source`。否則在你執行它時，新的 bash 進程將處理函數定義，而不是當前的 shell。

<!-- ## What are the places where various packages and tools are stored and how does referencing them work? What even is `/bin` or `/lib`? -->
## 各種程式套件和工具的儲存位置是什麼？如何引用它們？ `/bin` 和 `/lib` 又是什麼？

<!-- Regarding programs that you execute in your terminal, they are all found in the directories listed in your `PATH` environment variable and you can use the `which` command (or the `type` command) to check where your shell is finding a specific program.
In general, there are some conventions about where specific types of files live. Here are some of the ones we talked about, check the [Filesystem, Hierarchy Standard](https://en.wikipedia.org/wiki/Filesystem_Hierarchy_Standard) for a more comprehensive list.   -->
根據你在終端執行的程式，這些套件和工具會在你的 `PATH` 環境變量列出的地方找到。你可以使用 `which` 指令 (或者 `type` ) 來檢查你的 shell 在哪裏找到了這個程式。
通常，特定種類的檔案儲存有特定規範，[檔案系統階層標準](https://zh.wikipedia.org/zh-hk/%E6%96%87%E4%BB%B6%E7%B3%BB%E7%BB%9F%E5%B1%82%E6%AC%A1%E7%BB%93%E6%9E%84%E6%A0%87%E5%87%86)詳細介紹了這些。

<!-- - `/bin` - Essential command binaries
- `/sbin` - Essential system binaries, usually to be run by root
- `/dev` - Device files, special files that often are interfaces to hardware devices
- `/etc` - Host-specific system-wide configuration files
- `/home` - Home directories for users in the system
- `/lib` - Common libraries for system programs
- `/opt` - Optional application software
- `/sys` - Contains information and configuration for the system (covered in the [first lecture](/2020/course-shell/))
- `/tmp` - Temporary files (also `/var/tmp`). Usually deleted between reboots.
- `/usr/` - Read only user data
  + `/usr/bin` - Non-essential command binaries
  + `/usr/sbin` - Non-essential system binaries, usually to be run by root
  + `/usr/local/bin` - Binaries for user compiled programs
- `/var` - Variable files like logs or caches -->
- `/bin` - 需要在單用戶模式可用的必要命令（可執行檔案）
- `/sbin` - 必要的系統二進制檔案
- `/dev` - 必要裝置
- `/etc` - 特定主機，系統範圍內的設定檔
- `/home` - 用戶的家目錄
- `/lib` - /bin/ 和 /sbin/中二進制檔案必要的庫檔案。
- `/opt` - 可選應用軟件包
- `/sys` - 系統信息與配置文件 (在[第一課](/2020/course-shell/)中講到過)
- `/tmp` - 臨時檔案(同 /var/tmp)，在系統重新啟動時目錄中檔案不會被保留。
- `/usr/` - 用於儲存唯讀用戶數據的第二層次，只讀
  + `/usr/bin` - 非必要可執行檔案
  + `/usr/sbin` - 非必要的系統二進制檔案
  + `/usr/local/bin` - 用戶層級，編譯好的程式
- `/var` - 變數檔案——在正常執行的系統中其內容不斷變化的檔案

<!-- ## Should I `apt-get install` a python-whatever, or `pip install` whatever package? -->
## 我是否可以 `apt-get install` 一個 python 程式，或者使用 `pip install`?

<!-- There's no universal answer to this question. It's related to the more general question of whether you should use your system's package manager or a language-specific package manager to install software. A few things to take into account: -->
這個問題通常沒有合適的答案。這與使用作業系統套件管理系統還是特定語言的套件管理系統這個更打的問題有關。你需要考慮這些事情：

<!-- - Common packages will be available through both, but less popular ones or more recent ones might not be available in your system package manager. In this, case using the language-specific tool is the better choice.
- Similarly, language-specific package managers usually have more up to date versions of packages than system package managers.
- When using your system package manager, libraries will be installed system wide. This means that if you need different versions of a library for development purposes, the system package manager might not suffice. For this scenario, most programming languages provide some sort of isolated or virtual environment so you can install different versions of libraries without running into conflicts. For Python, there's virtualenv, and for Ruby, there's RVM.
- Depending on the operating system and the hardware architecture, some of these packages might come with binaries or might need to be compiled. For instance, in ARM computers like the Raspberry Pi, using the system package manager can be better than the language specific one if the former comes in form of binaries and the later needs to be compiled. This is highly dependent on your specific setup. -->
- 常見的套件都可以通過這兩種方法獲得，但是不常見或者較新的套件可能不在系統套件管理系統中。這種情況下，使用特定語言的管理系統是更好的選擇。
- 同樣，特定語言的管理系統提供的套件通常更新。
- 當使用系統套件管理系統時，會在系統範圍內安裝。這意味著如果你出於開發目的需要不同版本的庫，系統套件管理系統可能不會滿足你的需求。對於這種情況，大部分程式語言都提供了隔離或虛擬環境，因此你可以使用特定語言的管理系統安裝不同版本的庫而不會發生衝突。對於 Python， 使用 virtualenv；對於 Ruby，使用RVM。
- 取決於作業系統和硬體結構，一些套件可能會以二進位形式存在，或是需要被編譯。例如，在 Raspberry Pi 等 ARM 設備上，系統套件管理器可能會使用二進位套件，而特定語言的管理器則需要編譯。此時系統管理器往往較優。這很大程度上取決於你的特定設定。

<!-- You should try to use one solution or the other and not both since that can lead to conflicts that are hard to debug. Our recommendation is to use the language-specific package manager whenever possible, and to use isolated environments (like Python's virtualenv) to avoid polluting the global environment.  -->
你應該嘗試使用一種解決方案，而不是同時使用兩種，以避免難以調試的衝突。
我們建議儘可能使用特定於語言的管理系統，並使用隔離的環境（比如 Python 的 virtualenv）來避免污染全域。

<!-- ## What's the easiest and best profiling tools to use to improve performance of my code? -->
## 提升程式碼性能最簡單好用的分析工具有哪些

<!-- The easiest tool that is quite useful for profiling purposes is [print timing](/2020/debugging-profiling/#timing).
You just manually compute the time taken between different parts of your code. By repeatedly doing this, you can effectively do a binary search over your code and find the segment of code that took the longest.  -->
最簡單且十分有效的工具是 [print timing](/2020/debugging-profiling/#timing)。
你僅需手動計算程式不同部分消耗的時間，並重複這個過程，透過二分搜尋法找到耗時最長的部分。

<!-- For more advanced tools, Valgrind's [Callgrind](http://valgrind.org/docs/manual/cl-manual.html) lets you run your program and measure how long everything takes and all the call stacks, namely which function called which other function. It then produces an annotated version of your program's source code with the time taken per line. However, it slows down your program by an order of magnitude and does not support threads. For other cases, the [`perf`](http://www.brendangregg.com/perf.html) tool and other language specific sampling profilers can output useful data pretty quickly. [Flamegraphs](http://www.brendangregg.com/flamegraphs.html) are good visualization tool for the output of said sampling profilers. You should also try to use specific tools for the programming language or task you are working with. For example, for web development, the dev tools built into Chrome and Firefox have fantastic profilers. -->
若想尋找更高級的工具，Valgrind 的 [Callgrind](http://valgrind.org/docs/manual/cl-manual.html) 可以使你執行程式並計算所有的時間花費，並列出所有呼叫堆棧，即哪個函數呼叫了另一個。然後，它會生成帶註釋的程式碼，其中包含每列消耗的時間。但是它會拖慢程式速度一個數量級且不支援多線程。
還有 [`perf`](http://www.brendangregg.com/perf.html) 工具和其他語言的特定採樣分析器可以迅速給出數據。 [Flamegraphs](http://www.brendangregg.com/flamegraphs.html) 是對採樣分析器的可視化工具。你還可以使用針對特定語言或任務開發的工具，例如，對於網頁開發，Chrome 與 Firefox 內建的開發者工具有出色的性能分析器。

<!-- Sometimes the slow part of your code will be because your system is waiting for an event like a disk read or a network packet. In those cases, it is worth checking that back-of-the-envelope calculations about the theoretical speed in terms of hardware capabilities do not deviate from the actual readings. There are also specialized tools to analyze the wait times in system calls. These include tools like [eBPF](http://www.brendangregg.com/blog/2019-01-01/learn-ebpf-tracing.html) that perform kernel tracing of user programs. In particular [`bpftrace`](https://github.com/iovisor/bpftrace) is worth checking out if you need to perform this sort of low level profiling. -->
有時候，程式最慢的部分是系統等待讀取硬碟或網絡包。此時，需要檢查根據硬體性能估計的理論速度是否與實際速度相符。也有專用工具來分析系統呼叫中的等待時間，比如用於跟蹤使用者程式內核的 [eBPF](http://www.brendangregg.com/blog/2019-01-01/learn-ebpf-tracing.html)。如果需要底層的性能分析，[`bpftrace`](https://github.com/iovisor/bpftrace) 是個好選擇。

<!-- ## What browser plugins do you use? -->
## 你在用什麼瀏覽器外掛

<!-- Some of our favorites, mostly related to security and usability: -->
以下是我們喜歡的外掛，多數與安全性和易用性有關：

<!-- - [uBlock Origin](https://github.com/gorhill/uBlock) - It is a [wide-spectrum](https://github.com/gorhill/uBlock/wiki/Blocking-mode) blocker that doesn’t just stop ads, but all sorts of third-party communication a page may try to do. This also cover inline scripts and other types of resource loading. If you’re willing to spend some time on configuration to make things work, go to [medium mode](https://github.com/gorhill/uBlock/wiki/Blocking-mode:-medium-mode) or even [hard mode](https://github.com/gorhill/uBlock/wiki/Blocking-mode:-hard-mode). Those will make some sites not work until you’ve fiddled with the settings enough, but will also significantly improve your online security. Otherwise, the [easy mode](https://github.com/gorhill/uBlock/wiki/Blocking-mode:-easy-mode) is already a good default that blocks most ads and tracking. You can also define your own rules about what website objects to block.
- [Stylus](https://github.com/openstyles/stylus/) - a fork of Stylish (don't use Stylish, it was shown to [steal users browsing history](https://www.theregister.co.uk/2018/07/05/browsers_pull_stylish_but_invasive_browser_extension/)), allows you to sideload custom CSS stylesheets to websites. With Stylus you can easily customize and modify the appearance of websites. This can be removing a sidebar, changing the background color or even the text size or font choice. This is fantastic for making websites that you visit frequently more readable. Moreover, Stylus can find styles written by other users and published in [userstyles.org](https://userstyles.org/). Most common websites have one or several dark theme stylesheets for instance. 
- Full Page Screen Capture - Built into Firefox and [Chrome extension](https://chrome.google.com/webstore/detail/full-page-screen-capture/fdpohaocaechififmbbbbbknoalclacl?hl=en). Let's you take a screenshot of a full website, often much better than printing for reference purposes.
- [Multi Account Containers](https://addons.mozilla.org/en-US/firefox/addon/multi-account-containers/) - lets you separate cookies into "containers", allowing you to browse the web with different identities and/or ensuring that websites are unable to share information between them.
- Password Manager Integration - Most password managers have browser extensions that make inputting your credentials into websites not only more convenient but also more secure. Compared to simply copy-pasting your user and password, these tools will first check that the website domain matches the one listed for the entry, preventing phishing attacks that impersonate popular websites to steal credentials.  -->
- [uBlock Origin](https://github.com/gorhill/uBlock) - 這是一個[用途廣泛](https://github.com/gorhill/uBlock/wiki/Blocking-mode) 的封鎖器。它可以阻擋廣告與所有種類的第三方交互。它也可以阻擋頁內腳本與其他類型的資源加載。如果你願意花些時間配置來讓他工作地更好，嘗試[中等模式](https://github.com/gorhill/uBlock/wiki/Blocking-mode:-medium-mode)或者[嚴厲模式](https://github.com/gorhill/uBlock/wiki/Blocking-mode:-hard-mode)。這些設定會在你完成配置之前阻止一些站點正常工作，但是會顯著提升安全性。或者，[簡單模式](https://github.com/gorhill/uBlock/wiki/Blocking-mode:-easy-mode)作爲預設已經足夠優秀，並能阻擋多數廣告與追蹤。你也可以自訂規則來攔截頁面內容。
- [Stylus](https://github.com/openstyles/stylus/) - Stylish 的一個分支 (不要使用 Stylish，它會 [偷竊用戶瀏覽記錄](https://www.theregister.co.uk/2018/07/05/browsers_pull_stylish_but_invasive_browser_extension/))，它允許你旁加載自訂 CSS 樣式。你可以利用 Stylus 輕鬆更改網頁外觀。它可以刪除側邊欄，更改北京顏色，甚至更改文字大小與字型。這對於提升你常訪問的網頁的可讀性非常有用。此外，Stylus 支援其他用戶發佈在 [userstyles.org](https://userstyles.org/) 上的樣式表。例如，多數常用站點都有一個或多個深色樣式。
- 全頁面捕獲 - 在 Firefox 中內建，且有適用的 [Chrome extension](https://chrome.google.com/webstore/detail/full-page-screen-capture/fdpohaocaechififmbbbbbknoalclacl?hl=en)。這讓你可以擷取全頁面截圖，在用作參考時通常比印出來要好得多。
- [Multi Account Containers](https://addons.mozilla.org/en-US/firefox/addon/multi-account-containers/) - 允許你將 cookies 分離至單獨的“容器”，使你可以以不同的身份瀏覽網頁和/或確保站點之間無法共享訊息。
- 密碼管理器集成 - 多數密碼管理器都會有瀏覽器擴充來幫助你輸入憑據，它們方便又安全。比起單純複製-貼上你的名稱與密碼，這些工具會線確認網頁域名與庫中匹配，阻止假站點竊取登錄憑據。

<!-- ## What are other useful data wrangling tools? -->
## 還有哪些有用的資料預處理工具

<!-- Some of the data wrangling tools we did not have time to cover during the data wrangling lecture include `jq` or `pup` which are specialized parsers for JSON and HTML data respectively. The Perl programming language is another good tool for more advanced data wrangling pipelines. Another trick is the `column -t` command that can be used to convert whitespace text (not necessarily aligned) into properly column aligned text. -->
有些資料整理工具由於時間緊湊，在資料預處理課上我們沒有提及。包括 `jq` 與 `pup` 這些分別用於 JSON 和 HTML 資料的專有解析器。Perl 程式語言也是適用於高階資料整理的管道工具。還有一個技巧是使用 `column -t` 指令，將不一定對齊的空格分隔文本轉化成對齊的文本。

<!-- More generally a couple of more unconventional data wrangling tools are vim and Python. For some complex and multi-line transformations, vim macros can be a quite invaluable tools to use. You can just record a series of actions and repeat them as many times as you want, for instance in the editors [lecture notes](/2020/editors/#macros) (and last year's [video](/2019/editors/)) there is an example of converting a XML-formatted file into JSON just using vim macros. -->
通常來說，Vim 和 Python 也可以看作不常規的資料整理工具。對於一些複雜的多行轉換，Vim 巨集是非常寶貴的工具。你可以記錄一系列動作，並根據需求重複多次。例如，在編輯器章節的 [lecture notes](/2020/editors/#巨集) (和去年的[課程回放](/2019/editors/))中，有一個示例，僅使用vim巨集將XML格式的檔案轉換為JSON。

<!-- For tabular data, often presented in CSVs, the [pandas](https://pandas.pydata.org/) Python library is a great tool. Not only because it makes it quite easy to define complex operations like group by, join or filters; but also makes it quite easy to plot different properties of your data. It also supports exporting to many table formats including XLS, HTML or LaTeX. Alternatively the R programming language (an arguably [bad](http://arrgh.tim-smith.us/) programming language) has lots of functionality for computing statistics over data and can be quite useful as the last step of your pipeline. [ggplot2](https://ggplot2.tidyverse.org/) is a great plotting library in R.  -->
對於通常以CSV格式表示的表格數據，[pandas]（https://pandas.pydata.org/）Python庫是一個很好的工具。 因為它不僅使定義復雜的操作（如分組，聯接或過濾）變得非常容易；而且還很容易繪製資料的不同屬性。它還支持導出為多種表格式，包括XLS，HTML或LaTeX。 另外，R程式語言（可以說是[不好的]（http://arrgh.tim-smith.us/）程式語言）具有用於計算資料統計信息的許多功能，在作爲管道的最後一步時非常有效。 [ggplot2]（https://ggplot2.tidyverse.org/）是R中不錯的繪圖庫。

<!-- ## What is the difference between Docker and a Virtual Machine? -->
## Docker 和虛擬機有什麼區別

<!-- Docker is based on a more general concept called containers. The main difference between containers and virtual machines is that virtual machines will execute an entire OS stack, including the kernel, even if the kernel is the same as the host machine. Unlike VMs, containers avoid running another instance of the kernel and instead share the kernel with the host. In Linux, this is achieved through a mechanism called LXC, and it makes use of a series of isolation mechanism to spin up a program that thinks it's running on its own hardware but it's actually sharing the hardware and kernel with the host. Thus, containers have a lower overhead than a full VM. 
On the flip side, containers have a weaker isolation and only work if the host runs the same kernel. For instance if you run Docker on macOS, Docker needs to spin up a Linux virtual machine to get an initial Linux kernel and thus the overhead is still significant. Lastly, Docker is a specific implementation of containers and it is tailored for software deployment. Because of this, it has some quirks: for example, Docker containers will not persist any form of storage between reboots by default. -->
Docker基於一個名爲容器的更通用的概念。容器與虛擬機之間的主要區別在於，即使內核與主機相同，虛擬機也將執行整個OS堆棧，包括內核。與其不同，容器避免運行內核的另一個實例，而是與主機共享內核。在Linux中，這是通過稱為LXC的機制來實現的，它利用一系列隔離機制來啟動一個程式，該程式認為其在自己的硬件上運行，但實際上與主機共享硬體和內核。因此，容器的開銷要低於完整的虛擬機。
另一方面，容器的隔離性較弱，只有在主機運行相同的內核時容器才能工作。例如，如果你在 macOS 上運行 Docker，則 Docker 需要啟動 Linux 虛擬機以獲取初始 Linux 內核，因此開銷仍然很大。最後，Docker 是容器的特定實現，它是為軟體部署量身定制的。因此有一些奇怪之處：例如，默認情況下，Docker 容器在兩次重啟之間將不會保存任何形式的存儲。

<!-- ## What are the advantages and disadvantages of each OS and how can we choose between them (e.g. choosing the best Linux distribution for our purposes)? -->
## 每個作業系統的優缺點是什麼，我們如何在它們之間進行選擇（例如，為我們的目的選擇最適用的Linux發行版）？

<!-- Regarding Linux distros, even though there are many, many distros, most of them will behave fairly identically for most use cases. 
Most of Linux and UNIX features and inner workings can be learned in any distro. 
A fundamental difference between distros is how they deal with package updates. 
Some distros, like Arch Linux, use a rolling update policy where things are bleeding-edge but things might break every so often. On the other hand, some distros like Debian, CentOS or Ubuntu LTS releases are much more conservative with releasing updates in their repositories so things are usually more stable at the expense of sacrificing newer features. 
Our recommendation for an easy and stable experience with both desktops and servers is to use Debian or Ubuntu. -->
即使有很多不同版本的 Linux 發行版，大部分發行版在大多數使用情況下的表現也相當相同。
大部分 Linux 和 UNIX 功能以及內部工作原理都可以在任何發行版中學習。
發行版之間的根本區別是發行版如何處理軟件套件更新。
某些發行版（例如 Arch Linux）使用滾動更新策略，在這種策略中，套件更新激進，也常常導致不穩定的問題。 另一方面，一些發行版（如 Debian，CentOS 或 Ubuntu LTS）發布更新要保守得多，因此通常情況下會更穩定，但要犧牲一些新功能。
我們建議使用 Debian 或 Ubuntu 來獲得輕鬆穩定的台式機和服務器體驗。

<!-- Mac OS is a good middle point between Windows and Linux that has a nicely polished interface. However, Mac OS is based on BSD rather than Linux, so some parts of the system and commands are different.
An alternative worth checking is FreeBSD. Even though some programs will not run on FreeBSD, the BSD ecosystem is much less fragmented and better documented than Linux. 
We discourage Windows for anything but for developing Windows applications or if there is some deal breaker feature that you need, like good driver support for gaming.  -->
MacOS 是 Windows 和 Linux 之間的一個很好的折衷方案，它有靚麗的介面。 但是，MacOS 是基於 BSD 而不是 Linux ，因此系統的某些部分和命令會有所不同。
另一個值得一試的是 FreeBSD 。 即使某些程式不能在 FreeBSD 上運行，但與 Linux 相比，BSD 生態系統的碎片化程度要低得多，而且文檔更爲友好。
除了開發 Windows 應用程序或需要某些僅限 Windows支援的功能，例如對遊戲的驅動支援外，我們不建議使用Windows。

<!-- For dual boot systems, we think that the most working implementation is macOS' bootcamp and that any other combination can be problematic  on the long run, specially if you combine it with other features like disk encryption.  -->
對於雙系統，我們認為最有效的方案是 macOS 的 bootcamp，從長遠來看，任何其他組合都可能會出現問題，特別是將其與磁碟加密等其他功能結合使用時。

<!-- ## Vim vs Emacs? -->
## Vim 還是 Emacs

<!-- The three of us use vim as our primary editor but Emacs is also a good alternative and it's worth trying both to see which works better for you. Emacs does not follow vim's modal editing, but this can be enabled through Emacs plugins like [Evil](https://github.com/emacs-evil/evil) or [Doom Emacs](https://github.com/hlissner/doom-emacs). 
An advantage of using Emacs is that extensions can be implemented in Lisp, a better scripting language than vimscript, Vim's default scripting language. -->
我們三個人都使用 vim 作爲主要編輯器，不過 Emacs 也是一個很好的替代品，很值得去同時嘗試它們來看看哪個更適合你。Emacs 與 vim 的編輯模式不同，但是這些功能可以通過 Emacs 外掛，例如 [Evil](https://github.com/emacs-evil/evil) 和 [Doom Emacs](https://github.com/hlissner/doom-emacs) 實現。Emacs 的優勢是了一使用 Lisp 擴展。Lisp 是比 vim 默認使用的 vimscript 更優秀的腳本語言。

<!-- ## Any tips or tricks for Machine Learning applications? -->
## 有哪些機器學習的注意與技巧

<!-- Some of the lessons and takeaways from this class can directly be applied to ML applications. 
As it is the case with many science disciplines, in ML you often perform a series of experiments and want to check what things worked and what didn't. 
You can use shell tools to easily and quickly search through these experiments and aggregate the results in a sensible way. This could mean subselecting all experiments in a given time frame or that use a specific dataset. By using a simple JSON file to log all relevant parameters of the experiments, this can be incredibly simple with the tools we covered in this class. 
Lastly, if you do not work with some sort of cluster where you submit your GPU jobs, you should look into how to automate this process since it can be a quite time consuming task that also eats away your mental energy.  -->
此課程的一些經驗教訓可以直接應用於機器學習程式。
與許多科學學科的情況一樣，在機器學習中，經常要進行一系列實驗，並查看結果是否有效。
你可以使用 Shell 工具輕鬆，快速地搜索這些實驗結果並以明智的方式匯總它們。 這可能意味著在給定的時間內或使用特定數據集的情況下選擇所有實驗資料。透過使用JSON文件記錄實驗相關參數，使用我們在本課程中介紹的工具，這可以變得非常簡單。
最後，如果你不使用集群來處理你的 GPU 相關作業，則應該研究如何使該過程自動化，因為這不僅會耗費大量時間，還會顯著消耗你的耐心。

<!-- ## Any more Vim tips? -->
## 還有什麼 Vim 小技巧

<!-- A few more tips: -->
一點經驗：

<!-- - Plugins - Take your time and explore the plugin landscape. There are a lot of great plugins that address some of vim's shortcomings or add new functionality that composes well with existing vim workflows. For this, good resources are [VimAwesome](https://vimawesome.com/) and other programmers' dotfiles.
- Marks - In vim, you can set a mark doing `m<X>` for some letter `X`. You can then go back to that mark doing `'<X>`. This let's you quickly navigate to specific locations within a file or even across files. 
- Navigation - `Ctrl+O` and `Ctrl+I` move you backward and forward respectively through your recently visited locations.
- Undo Tree - Vim has a quite fancy mechanism for keeping track of changes. Unlike other editors, vim stores a tree of changes so even if you undo and then make a different change you can still go back to the original state by navigating the undo tree. Some plugins like [gundo.vim](https://github.com/sjl/gundo.vim) and [undotree](https://github.com/mbbill/undotree) expose this tree in a graphical way. 
- Undo with time - The `:earlier` and `:later` commands will let you navigate the files using time references instead of one change at a time.
- [Persistent undo](https://vim.fandom.com/wiki/Using_undo_branches#Persistent_undo) is an amazing built-in feature of vim that is disabled by default. It persists undo history between vim invocations. By setting `undofile` and `undodir` in your `.vimrc`, vim will storage a per-file history of changes.
- Leader Key - The leader key is a special key that is often left to the user to be configured for custom commands. The pattern is usually to press and release this key (often the space key) and then some other key to execute a certain command. Often, plugins will use this key to add their own functionality, for instance the UndoTree plugin uses `<Leader> U` to open the undo tree. 
- Advanced Text Objects - Text objects like searches can also be composed with vim commands. E.g. `d/<pattern>` will delete to the next match of said pattern or `cgn` will change the next occurrence of the last searched string.  -->
- 外掛 - 花些時間去探索外掛。有許多優秀的外掛解決了 Vim 的缺點，或者增加了能與現有工作流結合的新功能。關於這部分，你可以在 [VimAwesome](https://vimawesome.com/) 與其他工程師的 dotfiles 中找到優秀資源。
- 記號 - 在 Vim 中，你可以透過 `m<X>` 將字元 `X` 設定爲記號。其後可以使用 `'<X>` 來回到標記位置。這可以讓你在單一檔案內甚至跨檔案迅速定位。
- 導航 - `Ctrl+O` 與 `Ctrl+I` 可以在你最近瀏覽的位置間移動。
- 撤銷樹 - vim 對於追蹤變化有非常奇異的機制。不同於其他編輯器，vim 存儲變化樹，因爲你在撤銷後做了一些更正，仍然可以透過撤銷樹的導航回到初始狀態。一些外掛可以可視化撤銷樹，例如  [gundo.vim](https://github.com/sjl/gundo.vim) 和 [undotree](https://github.com/mbbill/undotree)。
- 按時間撤銷 - `:earlier` 和 `:later` 可以使用相對時間前後更正，而非逐一修改。
- [永久重做（Persistent undo）](https://vim.fandom.com/wiki/Using_undo_branches#Persistent_undo) 是一個默認不啓用的 vim 內建功能。它會在多次 vim 啓動之間記錄撤銷歷史。透過設定 `.vimrc` 內的 `undofile` 和 `undodir`，vim 會爲每個文件記錄更正歷史。
- Leader 鍵 -  Leader 鍵是一個通常留給用戶自訂功能的特殊按鍵。通常是由按下後釋放這個鍵（通常是空格鍵）並與其他按鍵組合來實現特殊指令。通常插件也會使用這些按鍵增加他們的功能，例如，UndoTree 會使用 `<Leader> U` 來打開撤銷樹。
- 高階文本對象 - 文本對象例如檢索也可以使用 vim 指令規程。比如，`d/<pattern>` 會刪除下移除匹配 pattern 的字串，`cgn` 可以更改上次檢索的關鍵字。

<!-- ## What is 2FA and why should I use it? -->
## 雙重驗證（2FA）是什麼，我該如何使用它

<!-- Two Factor Authentication (2FA) adds an extra layer of protection to your accounts on top of passwords. In order to login, you not only have to know some password, but you also have to "prove" in some way you have access to some hardware device. In the most simple case, this can be achieved by receiving an SMS on your phone, although there are [known issues](https://www.kaspersky.com/blog/2fa-practical-guide/24219/) with SMS 2FA. A better alternative we endorse is to use a [U2F](https://en.wikipedia.org/wiki/Universal_2nd_Factor) solution like [YubiKey](https://www.yubico.com/). -->
雙重驗證在密碼之上給予使用者賬戶額外的保護。登入時，你不僅需要密碼，還必須以某種方式“證明”你可以訪問某些硬體裝置。最簡單的情況是接收 SMS，儘管 SMS 已經被證明[有安全性問題](https://www.kaspersky.com/blog/2fa-practical-guide/24219/)。一個良好替代就是使用 [U2F](https://en.wikipedia.org/wiki/Universal_2nd_Factor) 方案，比如 [YubiKey](https://www.yubico.com/)。

<!-- ## Any comments on differences between web browsers? -->
## 對Web瀏覽器之間的差異有何評論

截至2020年，當前瀏覽器的格局是大多數瀏覽器都與 Chrome 很相像，因為它們使用相同的引擎（Blink）。這意味著同樣基於 Blink 的 Microsoft Edge 和基於 WebKit（與 Blink 類似的引擎）的 Safari 都是 Chrome 的較差版本。就性能和可用性而言，Chrome 是一款相當不錯的瀏覽器。如果你需要替代，建議使用Firefox。在幾乎所有方面，它都可以與 Chrome 媲美，並且它在隱私保護方面也十分出色。
另一個名為 [Flow](https://www.ekioh.com/flow-browser/) 的瀏覽器尚未完成，但它正在實現一個新的渲染引擎，該引擎有望比當前的渲染引擎更快。