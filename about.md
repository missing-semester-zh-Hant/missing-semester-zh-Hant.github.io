---
layout: lecture
title: "爲什麼我們要開設此課程？"
---

<!-- During a traditional Computer Science education, chances are you will take
plenty of classes that teach you advanced topics within CS, everything from
Operating Systems to Programming Languages to Machine Learning. But at many
institutions there is one essential topic that is rarely covered and is instead
left for students to pick up on their own: computing ecosystem literacy. -->
傳統電腦專業課程着重解釋作業系統與機器學習等專業知識，而培養基本電腦素養這個重要課題卻常常需要學生自行探索。

<!-- Over the years, we have helped teach several classes at MIT, and over and over
we have seen that many students have limited knowledge of the tools available
to them. Computers were built to automate manual tasks, yet students often
perform repetitive tasks by hand or fail to take full advantage of powerful
tools such as version control and text editors. In the best case, this results
in inefficiencies and wasted time; in the worst case, it results in issues like
data loss or inability to complete certain tasks. -->
這些年，我們在MIT參與教授了許多課程，並且逐漸意識到許多學生對他們可使用的工具知之甚少。
電腦善於執行自動化任務，但學生們常常自己親自做重複性任務，或者沒有完全發揮出版本控制，文字編輯器等工具的強大能力。
好一些的情況下，他們只是效率降低並浪費了時間；更糟糕的情況下，這導致了資料損失或使學生無法完成指定任務。

<!-- These topics are not taught as part of the university curriculum: students are
never shown how to use these tools, or at least not how to use them
efficiently, and thus waste time and effort on tasks that _should_ be simple.
The standard CS curriculum is missing critical topics about the computing
ecosystem that could make students' lives significantly easier. -->
這些論題在大學內不會傳授：學生始終不知道如何使用他們，或者至少不知道如何高效地使用他們。
這浪費了時間，也 _本可以_ 使用更輕鬆的方式完成任務。
標準的電腦科學課程缺少了顯著減輕學生壓力的這門課程。

# The missing semester of your CS education

<!-- To help remedy this, we are running a class that covers all the topics we
consider crucial to be an effective computer scientist and programmer. The
class is pragmatic and practical, and it provides hands-on introduction to
tools and techniques that you can immediately apply in a wide variety of
situations you will encounter. The class is being run during MIT's "Independent
Activities Period" in January 2020 — a one-month semester that features shorter
student-run classes. While the lectures themselves are only available to MIT
students, we will provide all lecture materials along with video recordings of
lectures to the public. -->
爲了解決這個問題，我們執教了這門課程，涵蓋了所有我們認爲對成爲高效電腦科學家或者程式設計師而言重要的主題。
該課程將十分實用，爲你提供對於工和相關技術的詳細講解，使您可以立即在多種情況下使用這些東西。
此課程在 2020 年 1 月的 MIT "Independent Activities Period" 內舉行，這是爲期一個月的短學期，主要由學生開設。
講座本身只適用於 MIT 學生，但是我們會公開所有講座材料和課程回放。

<!-- If this sounds like it might be for you, here are some concrete
examples of what the class will teach: -->
如果看起來這門課很適合你，以下是一些教學內容的具體示例：

<!-- ## Command shell -->
## 命令列

<!-- How to automate common and repetitive tasks with aliases, scripts,
and build systems. No more copy-pasting commands from a text
document. No more "run these 15 commands one after the other". No
more "you forgot to run this thing" or "you forgot to pass this
argument". -->
如何使用別名、手稿語言與如何建立系統。
不要再“依次執行這 15 個指令”，不要再“你忘記執行這個了”，不要再“你忘記鍵入引數了”。

<!-- For example, searching through your history quickly can be a huge time saver. In the example below we show several tricks related to navigating your shell history for `convert` commands. -->
例如，快速搜尋歷史記錄可以節約你大量的時間。下面這個示例展示了利用 `convert` 來在shell中跳轉的幾個小技巧。

<video autoplay="autoplay" loop="loop" controls muted playsinline  oncontextmenu="return false;"  preload="auto"  class="demo">
  <source src="/static/media/demos/history.mp4" type="video/mp4">
</video>

<!-- ## Version control -->
## 版本控制

<!-- How to use version control _properly_, and take advantage of it to
save you from disaster, collaborate with others, and quickly find and
isolate problematic changes. No more `rm -rf; git clone`. No more
merge conflicts (well, fewer of them at least). No more huge blocks
of commented-out code. No more fretting over how to find what broke
your code. No more "oh no, did we delete the working code?!". We'll
even teach you how to contribute to other people's projects with pull
requests! -->
如何 _正確地_ 使用版本控制，利用它從災難中挽回損失，與他人合作，與快速找到導致問題的部分。
不要再 `rm -rf; git clone`，不要再製造合併衝突（呃，至少要少製造一點），不要再大量註釋程式碼，也不要再“靠北，我們是不是把有用的部分刪掉了”。
我們甚至會教授你如何通過　`pull request` 來向他人的項目做貢獻。

<!-- In the example below we use `git bisect` to find which commit broke a unit test and then we fix it with `git revert`. -->
在這個示例中，我們使用 `git bisect`來尋找哪個提交未通過單元測試，並透過 `git revert` 進行改正。

<video autoplay="autoplay" loop="loop" controls muted playsinline  oncontextmenu="return false;"  preload="auto"  class="demo">
  <source src="/static/media/demos/git.mp4" type="video/mp4">
</video>

<!-- ## Text editing -->
## 文字編輯

<!-- How to efficiently edit files from the command-line, both locally and
remotely, and take advantage of advanced editor features. No more
copying files back and forth. No more repetitive file editing. -->
如何在近端與遠端使用命令列高效編輯檔案，並使用編輯器的進階功能。
不要再來回拉取與複製文件，也不要再重複編輯檔案。

<!-- Vim macros are one of its best features, in the example below we quickly convert an html table to csv format using a nested vim macro. -->
Vim的巨集功能是它最好的功能之一，在下面這個示例中，我們使用 Vim 內嵌的巨集功能來快速講html表格轉換成了csv格式。

<video autoplay="autoplay" loop="loop" controls muted playsinline  oncontextmenu="return false;"  preload="auto"  class="demo">
  <source src="/static/media/demos/vim.mp4" type="video/mp4">
</video>

<!-- ## Remote machines -->
## 遠端電腦

<!-- How to stay sane when working with remote machines using SSH keys and
terminal multiplexing. No more keeping many terminals open just to
run two commands at once. No more typing your password every time you
connect. No more losing everything just because your Internet
disconnected or you had to reboot your laptop. -->
在使用 SSH key 與終端多路復用操作遠端電腦時如何保持清醒。
不要再爲了同時執行兩個指令打開多個終端，也不要在每次連接時鍵入密碼，更不要因爲網路斷開或者重啓手提時丟失所有資料。

<!-- In the example below we use `tmux` to keep sessions alive in remote servers and `mosh` to support network roaming and disconnection. -->
在下面的示例中我們利用 `tmux` 使得會話在遠端伺服器保持活躍，並透過 `mosh` 使其支援網絡漫遊與斷開連接。


<video autoplay="autoplay" loop="loop" controls muted playsinline  oncontextmenu="return false;"  preload="auto"  class="demo">
  <source src="/static/media/demos/ssh.mp4" type="video/mp4">
</video>

<!-- ## Finding files -->
## 尋找文件

<!-- How to quickly find files that you are looking for. No
more clicking through files in your project until you find the one
that has the code you want. -->
如何快速尋找你需要的檔案，而非逐個打開所有檔案直至找到你需要的程式碼。

<!-- In the example below we quickly look for files with `fd` and for code snippets with `rg`. We also quickly `cd` and `vim` recent/frequent files/folder using `fasd`. -->
在下面的示例中我們利用 `fd` 快速搜尋文件，並透過 `rg` 找到程式碼片段。我萌也用到了 `fasd` 來迅速 `cd` 與 `vim` 最近/常用的檔案/檔案夾。

<video autoplay="autoplay" loop="loop" controls muted playsinline  oncontextmenu="return false;"  preload="auto"  class="demo">
  <source src="/static/media/demos/find.mp4" type="video/mp4">
</video>

<!-- ## Data wrangling -->
## 資料處理

<!-- How to quickly and easily modify, view, parse, plot, and compute over
data and files directly from the command-line. No more copy pasting
from log files. No more manually computing statistics over data. No
more spreadsheet plotting. -->
如何快速且易用地在命令列中對資料與檔案進行編輯，查看，分詞，繪圖與計算。
不再從日誌中複製粘貼，不再手動統計資料，不再使用表格繪圖。


<!-- ## Virtual machines -->
## 虛擬機器

<!-- How to use virtual machines to try out new operating systems, isolate
unrelated projects, and keep your main machine clean and tidy. No
more accidentally corrupting your computer while doing a security
lab. No more millions of randomly installed packages with differing
versions. -->
如何使用虛擬機嘗試全新作業系統，分隔不相關的工程，保持主系統整潔，不再因爲進行安全性實驗導致電腦受損，且不再隨意安裝不同版本的軟件套件。

<!-- ## Security -->
## 安全性

<!-- How to be on the Internet without immediately revealing all of your
secrets to the world. No more coming up with passwords that match the
insane criteria yourself. No more unsecured, open WiFi networks. No
more unencrypted messaging. -->
如何在不暴露隱私的情況下使用網路，不再爲相處符合自己詭異規則的密碼煩惱，不再連接不安全的WiFi，不再傳送未加密訊息。

<!-- # Conclusion -->
# 結語

<!-- This, and more, will be covered across the 12 class lectures, each including an
exercise for you to get more familiar with the tools on your own. If you can't
wait for January, you can also take a look at the lectures from [Hacker
Tools](https://hacker-tools.github.io/lectures/), which we ran during IAP last
year. It is the precursor to this class, and covers many of the same topics. -->
這 12 節課會包含以上或者更多的內容，同時每課都提供了能幫你熟練使用這些工具的試驗。
如果你已經等不及到一月，也可以先瀏覽[Hacker Tools](https://hacker-tools.github.io/lectures/)上面的課程，這是我們去年所教授的。
這是此課程的前身，含有許多相同的主題。

<!-- We hope to see you in January, whether virtually or in person! -->
希望能在一月與你相見，無論是線上還是親臨現場！

Happy hacking,<br>
Anish, Jose, and Jon
