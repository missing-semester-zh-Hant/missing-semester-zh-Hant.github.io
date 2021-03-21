---
layout: lecture
title: "Shell工具與腳本語言"
date: 2019-01-14
ready: true
video:
  aspect: 56.25
  id: kgII-YWo3Zw
---

<!-- In this lecture, we will present some of the basics of using bash as a scripting language along with a number of shell tools that cover several of the most common tasks that you will be constantly performing in the command line. -->
在本課中，我們會展示基於bash的腳本語言的一些基本操作，以及一些常用的 shell 工具。它們將解決我們在使用命令列時遇到的常見問題。


<!-- # Shell Scripting -->
# Shell腳本

<!-- So far we have seen how to execute commands in the shell and pipe them together.
However, in many scenarios you will want to perform a series of commands and make use of control flow expressions like conditionals or loops. -->
截至目前我們已經學習了如何在 shell 中執行指令， 並且用管道組合它們。
但是，在許多場景下我們希望執行一系列指令，並且使用條件或迴圈等流程控制。

<!-- Shell scripts are the next step in complexity.
Most shells have their own scripting language with variables, control flow and its own syntax.
What makes shell scripting different from other scripting programming language is that it is optimized for performing shell-related tasks.
Thus, creating command pipelines, saving results into files, and reading from standard input are primitives in shell scripting, which makes it easier to use than general purpose scripting languages.
For this section we will focus on bash scripting since it is the most common. -->
Shell 腳本是更加複雜的解決辦法。
大部分 shell 都有自己的腳本語言，包括變數，流程控制與專屬自己的語法。
shell 腳本與其他腳本語言不同的是，它針對有關 shell 的任務進行最佳化。
因此，創建指令流程，將結果存儲至檔案，從標準輸入中獲取訊息都是 shell 腳本的內建能力，
這使得它比普通腳本語言更加容易使用。
此節我們會專注於bash腳本，因爲它最為普遍，

<!-- To assign variables in bash, use the syntax `foo=bar` and access the value of the variable with `$foo`.
Note that `foo = bar` will not work since it is interpreted as calling the `foo` program with arguments `=` and `bar`.
In general, in shell scripts the space character will perform argument splitting. This behavior can be confusing to use at first, so always check for that. -->
在bash中，使用 `foo=bar` 爲變數賦值，使用 `$foo` 來取得變數的值。
注意 `foo = bar` 並不會正常執行，因爲它將被解釋爲使用參數 `=` 和 `bar` 執行 `foo`。
總之，在shell腳本中空格會分隔參數。初次使用時可能會造成混淆，請務必多加檢查。

<!-- Strings in bash can be defined with `'` and `"` delimiters, but they are not equivalent.
Strings delimited with `'` are literal strings and will not substitute variable values whereas `"` delimited strings will. -->
Bash 中的字串通過 `'` 與 `"` 定界字元來定義，它們含義並不相同。
以 `'` 定界的字串中的變數僅代表其字元本身，而 `"` 中的變數會作爲變數對待。

```bash
foo=bar
echo "$foo"
# prints bar
echo '$foo'
# prints $foo
```

<!-- As with most programming languages, bash supports control flow techniques including `if`, `case`, `while` and `for`.
Similarly, `bash` has functions that take arguments and can operate with them. Here is an example of a function that creates a directory and `cd`s into it. -->
如同大多數程式語言，bash支持包含`if`, `case`, `while` 與 `for`等的流程控制。
類似地，`bash` 也支援函式，其可以獲取參數並執行。以下例子會一個建立新文件夾並 `cd` 進入該文件夾：

```bash
mcd () {
    mkdir -p "$1"
    cd "$1"
}
```

<!-- Here `$1` is the first argument to the script/function.
Unlike other scripting languages, bash uses a variety of special variables to refer to arguments, error codes, and other relevant variables. Below is a list of some of them. A more comprehensive list can be found [here](https://www.tldp.org/LDP/abs/html/special-chars.html).
- `$0` - Name of the script
- `$1` to `$9` - Arguments to the script. `$1` is the first argument and so on.
- `$@` - All the arguments
- `$#` - Number of arguments
- `$?` - Return code of the previous command
- `$$` - Process identification number (PID) for the current script
- `!!` - Entire last command, including arguments. A common pattern is to execute a command only for it to fail due to missing permissions; you can quickly re-execute the command with sudo by doing `sudo !!`
- `$_` - Last argument from the last command. If you are in an interactive shell, you can also quickly get this value by typing `Esc` followed by `.` -->
此處 `$1`代表腳本或函式獲取的第一個參數。
與其他腳本語言不同，bash使用了大量特殊變數來表示參數，錯誤碼與其他相關變數。
此處列舉了部分例子，更完整的列表可以參照[於此](https://www.tldp.org/LDP/abs/html/special-chars.html).
- `$0` - 腳本名稱
- `$1` to `$9` - 腳本參數。 `$1` 是第一個參數，以此類推。
- `$@` - 全部參數
- `$#` - 參數數量
- `$?` - 前一條指令的傳回值
- `$$` - 目前腳本的行程ID (Process identification number, PID) 
- `!!` - 完整的前一條指令，含有參數。一個常見情況是因爲權限錯誤導致指令失敗，此時可以使用 `sudo !!` 再嘗試一次。
- `$_` - 前一條指令的最後一個參數。如果你使用的是互動式shell，按下 `Esc` 後輸入 `.` 可以獲取它。

<!-- Commands will often return output using `STDOUT`, errors through `STDERR`, and a Return Code to report errors in a more script-friendly manner.
The return code or exit status is the way scripts/commands have to communicate how execution went.
A value of 0 usually means everything went OK; anything different from 0 means an error occurred. -->
指令通常會使用 `STDOUT` 來返回輸出, 使用 `STDERR` 返回錯誤, 與一個傳回值(Return Code)來更加友好地表示錯誤訊息。
腳本或單獨指令利用傳回值與退出狀態(exit status)的形式互相溝通。
通常，傳回值爲 0 表示一切正常，非0的傳回值表示發生了某種錯誤。

<!-- Exit codes can be used to conditionally execute commands using `&&` (and operator) and `||` (or operator), both of which are [short-circuiting](https://en.wikipedia.org/wiki/Short-circuit_evaluation) operators. Commands can also be separated within the same line using a semicolon `;`.
The `true` program will always have a 0 return code and the `false` command will always have a 1 return code.
Let's see some examples -->
退出碼可以與 `&&` (and operator) 與 `||` (or operator)共同使用，它們都是[短路求值](https://en.wikipedia.org/wiki/Short-circuit_evaluation) 運算子。
同一行內的多個指令可以使用 `;` 分隔。
程式 `true` 的傳回值永遠是0，`false`的傳回值永遠是1.
以下是一些例子：

```bash
false || echo "Oops, fail"
# Oops, fail

true || echo "Will not be printed"
#

true && echo "Things went well"
# Things went well

false && echo "Will not be printed"
#

true ; echo "This will always run"
# This will always run

false ; echo "This will always run"
# This will always run
```

<!-- Another common pattern is wanting to get the output of a command as a variable. This can be done with _command substitution_.
Whenever you place `$( CMD )` it will execute `CMD`, get the output of the command and substitute it in place.
For example, if you do `for file in $(ls)`, the shell will first call `ls` and then iterate over those values.
A lesser known similar feature is _process substitution_, `<( CMD )` will execute `CMD` and place the output in a temporary file and substitute the `<()` with that file's name. This is useful when commands expect values to be passed by file instead of by STDIN. For example, `diff <(ls foo) <(ls bar)` will show differences between files in dirs  `foo` and `bar`. -->
另一種常見的模式是以變數的形式獲取一個指令的輸出。我們可以透過 _指令替換(command substitution)_ 來做這件事。
當在腳本中使用 `$( CMD )` 時， `CMD` 會被執行，然後用它的執行結果替換掉 `$( CMD )` 。
例如，如果你執行 `for file in $(ls)` ，shell會首先執行 `ls` 然後遍歷其傳回值。
另一個不太常見的特性是 _行程替代 (process substitution)_， `<( CMD )` 將會執行 `CMD` 並且將結果存入臨時檔案中，並將 `<()` 替換成臨時檔案名稱。
這在我們希望傳回值通過檔案而非 STDIN 傳送時非常有用。
例如， `diff <(ls foo) <(ls bar)` 將會顯示文件夾 `foo` 與 `bar` 中的內容.

<!-- Since that was a huge information dump, let's see an example that showcases some of these features. It will iterate through the arguments we provide, `grep` for the string `foobar`, and append it to the file as a comment if it's not found. -->
既然我們已經講了這麼多，是時候看看這些特性的範例了。
這個範例將會使用 `grep` 搜尋字串 `foobar`，如果沒有找到，就將其作爲註釋加入到檔案中。

```bash
#!/bin/bash

echo "Starting program at $(date)" # date將會被替代為日期與時間

echo "Running program $0 with $# arguments with pid $$"

for file in "$@"; do
    grep foobar "$file" > /dev/null 2> /dev/null
    # 當字串沒有被找到，grep將會退出並返回狀態碼 1
    # 我們將標準輸出流(STDOUT)和標準錯誤流(STDERR)重新導向到Null，因為我們並不關心這些訊息
    if [[ $? -ne 0 ]]; then
        echo "File $file does not have any foobar, adding one"
        echo "# foobar" >> "$file"
    fi
done
```

<!-- In the comparison we tested whether `$?` was not equal to 0.
Bash implements many comparisons of this sort - you can find a detailed list in the manpage for [`test`](https://www.man7.org/linux/man-pages/man1/test.1.html).
When performing comparisons in bash, try to use double brackets `[[ ]]` in favor of simple brackets `[ ]`. Chances of making mistakes are lower although it won't be portable to `sh`. A more detailed explanation can be found [here](http://mywiki.wooledge.org/BashFAQ/031).

When launching scripts, you will often want to provide arguments that are similar. Bash has ways of making this easier, expanding expressions by carrying out filename expansion. These techniques are often referred to as shell _globbing_.
- Wildcards - Whenever you want to perform some sort of wildcard matching, you can use `?` and `*` to match one or any amount of characters respectively. For instance, given files `foo`, `foo1`, `foo2`, `foo10` and `bar`, the command `rm foo?` will delete `foo1` and `foo2` whereas `rm foo*` will delete all but `bar`.
- Curly braces `{}` - Whenever you have a common substring in a series of commands, you can use curly braces for bash to expand this automatically. This comes in very handy when moving or converting files. -->

在條件語句中，我們比較 `$?` 是否等於 0。
Bash實現了許多類似的比較操作，您可以查看 [`test 手冊`](http://man7.org/linux/man-pages/man1/test.1.html)。 
在bash中進行比較時，盡量使用雙方括號 `[[ ]]` 而不是單方括號 `[ ]`，這樣會降低出錯的機率，儘管這樣並不能相容於 `sh`。更詳細的說明參見[這裡](http://mywiki.wooledge.org/BashFAQ/031)。

當執行腳本時，我們經常需要提供形式類似的參數。 bash使我們可以輕鬆的實現這一操作，它可以根據文件擴展名展開表達式。這一技術被稱為shell的 _通配_（ _globbing_）
- 萬用字元 - 當你想要利用萬用字元進行匹配時，你可以分別使用 `?` 和 `*` 來匹配一個或任意個字符。例如，對於文件`foo`, `foo1`, `foo2`, `foo10` 和`bar`, `rm foo?`這條命令會刪除`foo1` 和`foo2` ，而`rm foo*` 則會刪除除了`bar`之外的所有文件。
- 大括號 - 當你有一系列的指令，其中包含一段共同子字串時，可以用大括號來自動展開這些命令。這在批量移動或轉換文件時非常方便。

```bash
convert image.{png,jpg}
# 會展開為
convert image.png image.jpg

cp /path/to/project/{foo,bar,baz}.sh /newpath
# 會展開為
cp /path/to/project/foo.sh /path/to/project/bar.sh /path/to/project/baz.sh /newpath

# 也可以結合通配使用
mv *{.py,.sh} folder
# 會移動所有 *.py 和 *.sh 文件


mkdir foo bar
# 下面命令會建立foo/a, foo/b, ... foo/h, bar/a, bar/b, ... bar/h這些文件
touch {foo,bar}/{a..h}
touch foo/x bar/y
# 顯示foo和bar文件的不同
diff <(ls foo) <(ls bar)
# 輸出
# < x
# ---
# > y
```

<!-- Lastly, pipes `|` are a core feature of scripting. Pipes connect one program's output to the next program's input. We will cover them more in detail in the data wrangling lecture. -->

<!-- Writing `bash` scripts can be tricky and unintuitive. There are tools like [shellcheck](https://github.com/koalaman/shellcheck) that will help you find errors in your sh/bash scripts.

Note that scripts need not necessarily be written in bash to be called from the terminal. For instance, here's a simple Python script that outputs its arguments in reversed order: -->

編寫 `bash` 腳本有時候會很彆扭和反直覺。例如 [shellcheck](https://github.com/koalaman/shellcheck)這樣的工具可以幫助你找到sh/bash腳本中的錯誤。

請注意，腳本並不一定只有用bash寫才能在終端裡調用。比如說，這是一段Python腳本，作用是將輸入的參數倒序輸出：

```python
#!/usr/local/bin/python
import sys
for arg in reversed(sys.argv[1:]):
    print(arg)
```

<!-- The kernel knows to execute this script with a python interpreter instead of a shell command because we included a [shebang](https://en.wikipedia.org/wiki/Shebang_(Unix)) line at the top of the script.
It is good practice to write shebang lines using the [`env`](https://www.man7.org/linux/man-pages/man1/env.1.html) command that will resolve to wherever the command lives in the system, increasing the portability of your scripts. To resolve the location, `env` will make use of the `PATH` environment variable we introduced in the first lecture.
For this example the shebang line would look like `#!/usr/bin/env python`.

Some differences between shell functions and scripts that you should keep in mind are:
- Functions have to be in the same language as the shell, while scripts can be written in any language. This is why including a shebang for scripts is important.
- Functions are loaded once when their definition is read. Scripts are loaded every time they are executed. This makes functions slightly faster to load, but whenever you change them you will have to reload their definition.
- Functions are executed in the current shell environment whereas scripts execute in their own process. Thus, functions can modify environment variables, e.g. change your current directory, whereas scripts can't. Scripts will be passed by value environment variables that have been exported using [`export`](https://www.man7.org/linux/man-pages/man1/export.1p.html)
- As with any programming language, functions are a powerful construct to achieve modularity, code reuse, and clarity of shell code. Often shell scripts will include their own function definitions. -->

shell知道去用python直譯器而不是shell命令來運行這段腳本，是因為腳本的開頭第一行的[shebang](https://en.wikipedia.org/wiki/Shebang_(Unix))。

在shebang行中使用[`env`](http://man7.org/linux/man-pages/man1/env.1.html) 命令是一種慣例，它會利用環境變數中的程式來解析該腳本，這樣就提高來您的腳本的可移植性。 `env` 會利用我們第一節講座中介紹過的`PATH` 環境變數來進行定位。 
例如，使用了`env`的shebang看上去時這樣的`#!/usr/bin/env python`。 

我們應該注意shell函數和腳本有以下的差異： 
- 函數只能用與shell使用相同的語言，腳本可以使用任意語言。因此在腳本中包含 `shebang` 是很重要的。 
- 函數僅在定義時被載入，腳本會在每次被執行時載入。這讓函數的載入比腳本略快一些，但每次修改函數定義，都要重新載入一次。 
- 函數會在當前的shell環境中執行，腳本會在獨立的行程中執行。因此，函數可以對環境變數進行更改，比如改變當前工作目錄，腳本則不行。腳本需要使用 [`export`](http://man7.org/linux/man-pages/man1/export.1p.html) 將環境變數導出，並將值傳遞給環境變數。 
- 與其他程式語言一樣，函數可以模組化程式碼、提高程式碼複用性與可讀性。 shell腳本中往往也會包含它們自己的函數定義。

# Shell 工具

## 查看命令如何使用

<!-- At this point, you might be wondering how to find the flags for the commands in the aliasing section such as `ls -l`, `mv -i` and `mkdir -p`.
More generally, given a command, how do you go about finding out what it does and its different options?
You could always start googling, but since UNIX predates StackOverflow, there are built-in ways of getting this information. -->

看到這裡，您可能會有疑問，我們應該如何找到該命令的旗標(flag)呢？例如 `ls -l`, `mv -i` 和 `mkdir -p`。
或者問說，給予一個指令，我們如何查詢他的使用方法與可設置的選項?
您可能會先去Google答案，但是，UNIX 可比 StackOverflow 出現的早，我們的系統裡其實早就內建方法可以查到相關訊息。

<!-- As we saw in the shell lecture, the first-order approach is to call said command with the `-h` or `--help` flags. A more detailed approach is to use the `man` command.
Short for manual, [`man`](https://www.man7.org/linux/man-pages/man1/man.1.html) provides a manual page (called manpage) for a command you specify.
For example, `man rm` will output the behavior of the `rm` command along with the flags that it takes, including the `-i` flag we showed earlier.
In fact, what I have been linking so far for every command is the online version of the Linux manpages for the commands.
Even non-native commands that you install will have manpage entries if the developer wrote them and included them as part of the installation process.
For interactive tools such as the ones based on ncurses, help for the commands can often be accessed within the program using the `:help` command or typing `?`. -->

在上一節中我們介紹過，最常用的方法是為對應的命令行添加`-h` 或 `--help` 旗標。另外一個更詳細的方法則是使用`man` 命令。 [`man`](http://man7.org/linux/man-pages/man1/man.1.html) 命令是手冊（manual）的縮寫，它提供了命令的用戶手冊。
例如，`man rm` 會輸出命令 `rm` 的說明，同時還有其標記列表，包括之前我們介紹過的`-i`。 
事實上，目前我們給出的所有命令的說明鏈接，都是網頁版的Linux命令手冊。即使是您安裝的第三方命令，前提是開發者編寫了手冊並將其包含在了安裝包中。在交互式的、基於字符處理的終端窗口中，一般也可以通過 `:help` 命令或輸入 `?`來獲取幫助。

<!-- Sometimes manpages can provide overly detailed descriptions of the commands, making it hard to decipher what flags/syntax to use for common use cases.
[TLDR pages](https://tldr.sh/) are a nifty complementary solution that focuses on giving example use cases of a command so you can quickly figure out which options to use.
For instance, I find myself referring back to the tldr pages for [`tar`](https://tldr.ostera.io/tar) and [`ffmpeg`](https://tldr.ostera.io/ffmpeg) way more often than the manpages. -->

有時候手冊內容太過詳實，讓我們難以在其中查詢哪些是最常用的旗標和語法。 
[TLDR pages](https://tldr.sh/) 是一個很不錯的替代品，它提供了一些案例，可以幫助您快速找到正確的選項。 例如，自己就常常在tldr上搜尋[`tar`](https://tldr.ostera.io/tar) 和 [`ffmpeg`](https://tldr.ostera.io/ffmpeg) 的用法。

## 查詢文件

<!-- One of the most common repetitive tasks that every programmer faces is finding files or directories.
All UNIX-like systems come packaged with [`find`](https://www.man7.org/linux/man-pages/man1/find.1.html), a great shell tool to find files. `find` will recursively search for files matching some criteria. Some examples: -->

程式設計師們面對的最常見的重複任務就是查詢文件或目錄。所有的類UNIX系統都包含一個名為[`find`](http://man7.org/linux/man-pages/man1/find.1.html)的工具，它是shell上用於查詢文件的絕佳工具。 `find`命令會遞歸地搜尋符合條件的文件，例如：

```bash
# 查詢所有名稱為src的文件夾
find . -name src -type d
# 查詢所有文件夾路徑中包含test的python文件
find . -path '*/test/*.py' -type f
# 查詢前一天修改的所有文件
find . -mtime -1
# 查詢所有大小在500k至10M的tar.gz文件
find . -size +500k -size -10M -name '*.tar.gz'
```

<!-- Beyond listing files, find can also perform actions over files that match your query.
This property can be incredibly helpful to simplify what could be fairly monotonous tasks. -->
除了列出所尋找的文件之外，find還能對所有查詢到的文件進行操作。這能大大簡化一些單調的任務。


```bash
# 刪除所有副檔名為 .tmp 的文件
find . -name '*.tmp' -exec rm {} \;
# 轉換所有 PNG 檔為 JPG 檔
find . -name '*.png' -exec convert {} {}.jpg \;
```

<!-- Despite `find`'s ubiquitousness, its syntax can sometimes be tricky to remember.
For instance, to simply find files that match some pattern `PATTERN` you have to execute `find -name '*PATTERN*'` (or `-iname` if you want the pattern matching to be case insensitive).
You could start building aliases for those scenarios, but part of the shell philosophy is that it is good to explore alternatives.
Remember, one of the best properties of the shell is that you are just calling programs, so you can find (or even write yourself) replacements for some.
For instance, [`fd`](https://github.com/sharkdp/fd) is a simple, fast, and user-friendly alternative to `find`.
It offers some nice defaults like colorized output, default regex matching, and Unicode support. It also has, in my opinion, a more intuitive syntax.
For example, the syntax to find a pattern `PATTERN` is `fd PATTERN`. -->
儘管 `find` 用途廣泛，它的語法卻比較難以記憶。
例如，為了查詢滿足的模式 `PATTERN` 的文件，您需要執行 `find -name '*PATTERN*'` (如果您希望模式匹配時是區分大小寫，可以使用`-iname`選項） 
您當然可以使用alias設置別名來簡化上述操作，但shell的哲學之一便是尋找（更好用的）替代方案。 
記住，shell最好的特性就是您只是在呼叫程式，因此您只要找到合適的替代程式即可（甚至自己編寫）。 
例如， [`fd`](https://github.com/sharkdp/fd) 就是一個更簡單、更快速、更友好的程式，它可以用來作為`find`的替代品。
它有很多不錯的默認設置，例如輸出著色、默認支持正則匹配、支持unicode並且我認為它的語法更符合直覺。以模式`PATTERN` 搜尋的語法是 `fd PATTERN`。

<!-- Most would agree that `find` and `fd` are good, but some of you might be wondering about the efficiency of looking for files every time versus compiling some sort of index or database for quickly searching.
That is what [`locate`](https://www.man7.org/linux/man-pages/man1/locate.1.html) is for.
`locate` uses a database that is updated using [`updatedb`](https://www.man7.org/linux/man-pages/man1/updatedb.1.html).
In most systems, `updatedb` is updated daily via [`cron`](https://www.man7.org/linux/man-pages/man8/cron.8.html).
Therefore one trade-off between the two is speed vs freshness.
Moreover `find` and similar tools can also find files using attributes such as file size, modification time, or file permissions, while `locate` just uses the file name.
A more in-depth comparison can be found [here](https://unix.stackexchange.com/questions/60205/locate-vs-find-usage-pros-and-cons-of-each-other). -->
大多數人都認為`find` 和`fd` 已經很好用了，但是有的人可能想知道，我們有沒有更有效的方法，例如不要每次都搜尋文件而是通過編譯索引或建立數據庫的方式來實現更加快速地搜尋。 這就要靠 [`locate`](http://man7.org/linux/man-pages/man1/locate.1.html) 了。 `locate` 使用一個由[`updatedb`](http://man7.org/linux/man-pages/man1/updatedb.1.html)負責更新的數據庫，在大多數係統中`updatedb` 都會通過[`cron`](http://man7.org/linux/man-pages/man8/cron.8.html)每日更新。這便需要我們在速度和時效性之間做出權衡。而且，`find` 和類似的工具可以通過別的屬性比如文件大小、修改時間或是權限來查詢文件，`locate`則只能通過文件名。 [這裡](https://unix.stackexchange.com/questions/60205/locate-vs-find-usage-pros-and-cons-of-each-other)有一個更詳細的對比。

## 查詢代碼

<!-- Finding files by name is useful, but quite often you want to search based on file *content*. 
A common scenario is wanting to search for all files that contain some pattern, along with where in those files said pattern occurs.
To achieve this, most UNIX-like systems provide [`grep`](https://www.man7.org/linux/man-pages/man1/grep.1.html), a generic tool for matching patterns from the input text.
`grep` is an incredibly valuable shell tool that we will cover in greater detail during the data wrangling lecture. -->
查詢文件是很有用的技能，但是很多時候您的目標其實是查看文件的內容。
一個最常見的場景是您希望查詢具有某種模式的全部文件，並找它們的位置。 為了實現這一點，很多類UNIX的系統都提供了[`grep`](http://man7.org/linux/man-pages/man1/grep.1.html)命令，它是用於對輸入文本進行匹配的通用工具。它是一個非常重要的shell工具，我們會在後續的資料預處理課程中深入的探討它。


<!-- For now, know that `grep` has many flags that make it a very versatile tool.
Some I frequently use are `-C` for getting **C**ontext around the matching line and `-v` for in**v**erting the match, i.e. print all lines that do **not** match the pattern. For example, `grep -C 5` will print 5 lines before and after the match.
When it comes to quickly searching through many files, you want to use `-R` since it will **R**ecursively go into directories and look for files for the matching string. -->
`grep` 有很多選項，這也使它成為一個非常全能的工具。
其中我經常使用的有 `-C` ：獲取查詢結果的上下文（**C**ontext）；`-v` 將對結果進行反選（In**v**ert），也就是輸出**不**匹配的結果。舉例來說， `grep -C 5` 會輸出匹配結果前後五行。
當需要搜索大量文件的時候，使用 `-R` 會遞歸地(**R**ecursively)進入子目錄並蒐索所有匹配的文件。

<!-- But `grep -R` can be improved in many ways, such as ignoring `.git` folders, using multi CPU support, &c.
Many `grep` alternatives have been developed, including [ack](https://beyondgrep.com/), [ag](https://github.com/ggreer/the_silver_searcher) and [rg](https://github.com/BurntSushi/ripgrep).
All of them are fantastic and pretty much provide the same functionality.
For now I am sticking with ripgrep (`rg`), given how fast and intuitive it is. Some examples: -->
但是，我們有很多辦法可以對 `grep -R` 進行改進，例如使其忽略`.git` 文件夾，使用多CPU等等。 
因此也出現了很多它的替代品，包括[ack](https://beyondgrep.com/), [ag](https://github.com/ggreer/the_silver_searcher) 和[rg](https:// github.com/BurntSushi/ripgrep)。
它們都非常好用，但是功能也都差不多，我比較常用的是 ripgrep (`rg`) ，因為它速度快，而且用法非常符合直覺。例子如下：

```bash
# 查詢所有使用了 requests 函式庫的文件
rg -t py 'import requests'
# 查詢所有沒有寫 shebang 的文件（包含隱藏文件）
rg -u --files-without-match "^#!"
# 查詢所有的foo字串，並印出其之後的5行
rg foo -A 5
# 印出匹配的統計信息（匹配的行和文件的數量）
rg --stats PATTERN
```

<!-- Note that as with `find`/`fd`, it is important that you know that these problems can be quickly solved using one of these tools, while the specific tools you use are not as important. -->
與 `find`/`fd` 一樣，重要的是你要知道有些問題使用合適的工具就會迎刃而解，而具體選擇哪個工具則不是那麼重要。

## 查詢 shell 命令

<!-- So far we have seen how to find files and code, but as you start spending more time in the shell, you may want to find specific commands you typed at some point.
The first thing to know is that the typing up arrow will give you back your last command, and if you keep pressing it you will slowly go through your shell history. -->
目前為止，我們已經學習瞭如何查找文件和代碼，但隨著你使用shell的時間越來越久，您可能想要找到之前輸入過的某條命令。
首先，按向上的方向鍵會顯示你使用過的上一條命令，繼續按上鍵則會遍歷整個歷史記錄。

<!-- The `history` command will let you access your shell history programmatically.
It will print your shell history to the standard output.
If we want to search there we can pipe that output to `grep` and search for patterns.
`history | grep find` will print commands that contain the substring "find". -->
`history` 命令允許我們查找過去shell中輸入的歷史命令。這個命令會在標準輸出中印出shell中的命令。
如果我們要搜索歷史記錄，則可以利用管道將輸出結果傳遞給 `grep` 進行模式搜索。 `history | grep find` 會打印包含find子串的命令。

<!-- In most shells, you can make use of `Ctrl+R` to perform backwards search through your history.
After pressing `Ctrl+R`, you can type a substring you want to match for commands in your history.
As you keep pressing it, you will cycle through the matches in your history.
This can also be enabled with the UP/DOWN arrows in [zsh](https://github.com/zsh-users/zsh-history-substring-search).
A nice addition on top of `Ctrl+R` comes with using [fzf](https://github.com/junegunn/fzf/wiki/Configuring-shell-key-bindings#ctrl-r) bindings.
`fzf` is a general-purpose fuzzy finder that can be used with many commands.
Here is used to fuzzily match through your history and present results in a convenient and visually pleasing manner. -->
對於大多數的shell來說，您可以使用 `Ctrl+R` 對命令歷史記錄進行回溯搜索。
按下 `Ctrl+R` 後您可以輸入子串來進行匹配，查找歷史命令行。重複按下就會在所有搜索結果中循環。
在 [zsh](https://github.com/zsh-users/zsh-history-substring-search)中，使用方向鍵上或下也可以完成這項工作。 
`Ctrl+R` 可以配合 [fzf](https://github.com/junegunn/fzf/wiki/Configuring-shell-key-bindings#ctrl-r) 使用。 
`fzf` 是一個通用的模糊查找工具，它可以和很多命令一起使用。這裡我們可以對歷史命令進行模糊查找並將結果以賞心悅目的格式輸出。

<!-- Another cool history-related trick I really enjoy is **history-based autosuggestions**.
First introduced by the [fish](https://fishshell.com/) shell, this feature dynamically autocompletes your current shell command with the most recent command that you typed that shares a common prefix with it.
It can be enabled in [zsh](https://github.com/zsh-users/zsh-autosuggestions) and it is a great quality of life trick for your shell. -->
另外一個和歷史命令相關的技巧我喜歡稱之為**基於歷史的自動補全**。 
這一特性最初是由 [fish](https://fishshell.com/) shell 創建的，它可以根據您最近使用過的開頭相同的命令，動態地對當前對shell命令進行補全。
這一功能在 [zsh](https://github.com/zsh-users/zsh-autosuggestions) 中也可以使用，它可以極大對提高用戶體驗。

<!-- Lastly, a thing to have in mind is that if you start a command with a leading space it won't be added to your shell history.
This comes in handy when you are typing commands with passwords or other bits of sensitive information.
If you make the mistake of not adding the leading space, you can always manually remove the entry by editing your `.bash_history` or `.zhistory`. -->
最後，有一點值得注意，輸入命令時，如果您在命令的開頭加上一個空格，它就不會被加進shell記錄中。當你輸入包含密碼或是其他敏感尋訊息的命令時會用到這一特性。
如果你不小心忘了在前面加空格，可以通過編輯 `bash_history` 或 `.zhistory` 來手動地從歷史記錄中移除那一項。

## 文件夾切換

<!-- So far, we have assumed that you are already where you need to be to perform these actions. But how do you go about quickly navigating directories?
There are many simple ways that you could do this, such as writing shell aliases or creating symlinks with [ln -s](https://www.man7.org/linux/man-pages/man1/ln.1.html), but the truth is that developers have figured out quite clever and sophisticated solutions by now. -->
目前的所有操作我們都假設一個前提，也就是我們已經位於想要執行命令的目錄下，但是如何才能高效地在目錄間隨意切換呢？
有很多簡便的方法可以做到，比如設置alias，使用 [ln -s](http://man7.org/linux/man-pages/man1/ln.1.html)創建符號連接等。而開發者們已經想到了很多更為巧妙的解決方案。


<!-- As with the theme of this course, you often want to optimize for the common case.
Finding frequent and/or recent files and directories can be done through tools like [`fasd`](https://github.com/clvv/fasd) and [`autojump`](https://github.com/wting/autojump).
Fasd ranks files and directories by [_frecency_](https://developer.mozilla.org/en/The_Places_frecency_algorithm), that is, by both _frequency_ and _recency_.
By default, `fasd` adds a `z` command that you can use to quickly `cd` using a substring of a _frecent_ directory. For example, if you often go to `/home/user/files/cool_project` you can simply use `z cool` to jump there. Using autojump, this same change of directory could be accomplished using `j cool`. -->
對於本課程的主題來說，我們希望對常用的情況進行最佳化。
使用[`fasd`](https://github.com/clvv/fasd)可以查找最常用和/或最近使用的文件和目錄。 
Fasd 根據 [_frecency_](https://developer.mozilla.org/en/The_Places_frecency_algorithm)對文件和文件排序，也就是說它會同時針對頻率（_frequency_ ）和時效（ _recency_）進行排序。 最直接對用法是自動跳轉 （_autojump_），對於經常訪問的目錄，在目錄名子串前加入一個命令 `z` 就可以快速切換命令到該目錄。例如， 如果您經常訪問`/home/user/files/cool_project` 目錄，那麼可以使用 `z cool` 直接進入該目錄。

<!-- More complex tools exist to quickly get an overview of a directory structure [`tree`](https://linux.die.net/man/1/tree), [`broot`](https://github.com/Canop/broot) or even full fledged file managers like [`nnn`](https://github.com/jarun/nnn) or [`ranger`](https://github.com/ranger/ranger) -->
還有一些更複雜的工具可以用來概覽目錄結構，例如[`tree`](https://linux.die.net/man/1/tree), [`broot`](https://github. com/Canop/broot) 或更加完整對文件管理器，例如[`nnn`](https://github.com/jarun/nnn) 或[`ranger`](https://github.com/ranger/ ranger)。

# 課後練習

1. 參考 [`man ls`](https://www.man7.org/linux/man-pages/man1/ls.1.html) 並撰寫 `ls` 指令來完成以下操作:

    - 所有文件（包括隱藏文件）
    - 文件打印以人類可以理解的格式輸出 (例如，使用 454M 而不是 454279954)
    - 文件以最近訪問順序排序
    - 以彩色文本顯示輸出結果

    典型輸出如下：

    ```
    -rw-r--r--   1 user group 1.1M Jan 14 09:53 baz
    drwxr-xr-x   5 user group  160 Jan 14 09:53 .
    -rw-r--r--   1 user group  514 Jan 14 06:42 bar
    -rw-r--r--   1 user group 106M Jan 13 12:12 foo
    drwx------+ 47 user group 1.5K Jan 12 18:08 ..
    ```

{% comment %}
ls -lath --color=auto
{% endcomment %}

2. 編寫兩個bash函數 `marco` 和 `polo` 執行下面的操作。
每當你執行 `marco` 時，當前的工作目錄應當以某種形式保存，當執行 `polo` 時，無論現在處在什麼目錄下，都應當 `cd` 回到當時執行 `marco` 的目錄。 為了方便debug，你可以把代碼寫在單獨的文件 `marco.sh` 中，並通過 `source marco.sh`命令，重新載入函數。

{% comment %}
marco() {
    export MARCO=$(pwd)
}

polo() {
    cd "$MARCO"
}
{% endcomment %}

3. 假設您有一個命令，它很少出錯。因此為了在出錯時能夠對其進行調試，需要花費大量的時間重現錯誤並捕獲輸出。 
編寫一段bash腳本，運行如下的腳本直到它出錯，將它的標準輸出和標準錯誤流記錄到文件，並在最後輸出所有內容。
Bonus: 報告腳本在失敗前共運行了多少次。

    ```bash
    #!/usr/bin/env bash

    n=$(( RANDOM % 100 ))

    if [[ n -eq 42 ]]; then
       echo "Something went wrong"
       >&2 echo "The error was using magic numbers"
       exit 1
    fi

    echo "Everything went according to plan"
    ```

{% comment %}
#!/usr/bin/env bash

count=0
until [[ "$?" -ne 0 ]];
do
  count=$((count+1))
  ./random.sh &> out.txt
done

echo "found error after $count runs"
cat out.txt
{% endcomment %}

4. 本節課我們講解了 `find` 命令的 `-exec` 參數非常強大，它可以對我們查找對文件進行操作。
但是，如果我們要對**所有**文件進行操作呢？例如創建一個zip壓縮文件？
我們已經知道，命令行可以從參數或標準輸入接受輸入。
在用管道連接命令時，我們將標準輸出和標準輸入連接起來，但是有些命令，例如 `tar` 則需要從參數接受輸入。這裡我們可以使用 [`xargs`](http://man7.org/linux/man-pages/man1/xargs.1.html) 命令，它可以使用標準輸入中的內容作為參數。 
例如 `ls | xargs rm` 會刪除當前目錄中的所有文件。

    您的任務是編寫一個命令，它可以遞歸地查找文件夾中所有的HTML文件，並將它們壓縮成zip文件。注意，即使文件名中包含空格，您的命令也應該能夠正確執行（提示：查看 `xargs`的參數`-d`）
    {% comment %}
    find . -type f -name "*.html" | xargs -d '\n'  tar -cvzf archive.tar.gz
    {% endcomment %}

5. (進階) 編寫一個命令或腳本遞歸的查找文件夾中最近使用的文件。更通用的做法，你可以按照最近的使用時間列出文件嗎？
