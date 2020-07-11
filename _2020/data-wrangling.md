---
layout: lecture
title: "資料預處理"
date: 2019-01-16
ready: true
video:
  aspect: 56.25
  id: sz_dsktIjt4
---

<!-- Have you ever wanted to take data in one format and turn it into a
different format? Of course you have! That, in very general terms, is
what this lecture is all about. Specifically, massaging data, whether in
text or binary format, until you end up with exactly what you wanted. -->
你是否有過變換資料形式的需求？當然有過！這也是此課會講授的內容。
具體來說，我們需要對文字或二進位形式的資料不斷處理，直至獲得我們所需的內容。

<!-- We've already seen some basic data wrangling in past lectures. Pretty
much any time you use the `|` operator, you are performing some kind of
data wrangling. Consider a command like `journalctl | grep -i intel`. It
finds all system log entries that mention Intel (case insensitive). You
may not think of it as wrangling data, but it is going from one format
(your entire system log) to a format that is more useful to you (just
the intel log entries). Most data wrangling is about knowing what tools
you have at your disposal, and how to combine them. -->
在之前部分的課程中，我們已經遇到過一些基礎的資料處理例項，比如當你使用 `|` 時，已經是在使用基本形式的資料處理了。
考慮這樣一個指令 `journalctl | grep -i intel`。它會搜尋所有提到Intel（大小寫敏感）的系統日誌。
你或許不認為這是資料處理，但這將資料從一種形式（全部系統日誌）轉換成了另一種更有價值的形式（僅含intel的日誌）。
大部分資料處理工作需要我們理解如何組合並使用工具來達成目的。

<!-- Let's start from the beginning. To wrangle data, we need two things:
data to wrangle, and something to do with it. Logs often make for a good
use-case, because you often want to investigate things about them, and
reading the whole thing isn't feasible. Let's figure out who's trying to
log into my server by looking at my server's log: -->
讓我們從頭說起。實行資料處理，需要兩個條件：用來處理的資料，以及處理的情境。
日誌處理是一個常見的情景，因為我們常常需要在日誌中尋找資訊，此時閱讀所有日誌是不現實的。
讓我們通過檢視日誌來找出有誰曾試圖登入我們的伺服器：

```bash
ssh myserver journalctl
```

<!-- That's far too much stuff. Let's limit it to ssh stuff: -->
內容太多了，讓我們只看與ssh相關的：

```bash
ssh myserver journalctl | grep sshd
```

<!-- Notice that we're using a pipe to stream a _remote_ file through `grep`
on our local computer! `ssh` is magical, and we will talk more about it
in the next lecture on the command-line environment. This is still way
more stuff than we wanted though. And pretty hard to read. Let's do
better: -->
請注意我們在此處通過 `grep` 來使用管道，將 _遠端的_ 檔案傳送至本地端電腦! 
`ssh` 非常神奇，我們會在下一課的命令列環境中詳細講授。
此時返回的內容依然冗長，且不好閱讀。讓我們做點改善：

```bash
ssh myserver 'journalctl | grep sshd | grep "Disconnected from"' | less
```

<!-- Why the additional quoting? Well, our logs may be quite large, and it's
wasteful to stream it all to our computer and then do the filtering.
Instead, we can do the filtering on the remote server, and then massage
the data locally. `less` gives us a "pager" that allows us to scroll up
and down through the long output. To save some additional traffic while
we debug our command-line, we can even stick the current filtered logs
into a file so that we don't have to access the network while
developing: -->
為什麼要使用雙層引用呢？
我們檢視的日誌非常多，從遠端傳送至本地端再濾掉有些浪費。
作為替代，我們可以在遠端就濾掉一部分，然後將其結果傳送至本地端。 
`less` 建立了一個 「分頁機制」 來允許我們通過滾動頁面來閱讀長文字。
如果想節省傳送流量，我們甚至可以將過濾後的日誌寫入檔案，使得我們不需要再次通過網路傳送：

```console
$ ssh myserver 'journalctl | grep sshd | grep "Disconnected from"' > ssh.log
$ less ssh.log
```

<!-- There's still a lot of noise here. There are _a lot_ of ways to get rid
of that, but let's look at one of the most powerful tools in your
toolkit: `sed`. -->
此時的結果依然有許多無用的部分。 我們有 _很多_ 方法來更優化。首先讓我們熟悉一下最強的工具之一: `sed`.

<!-- `sed` is a "stream editor" that builds on top of the old `ed` editor. In
it, you basically give short commands for how to modify the file, rather
than manipulate its contents directly (although you can do that too).
There are tons of commands, but one of the most common ones is `s`:
substitution. For example, we can write: -->
`sed` 是一個基於舊式 `ed` 文字編輯器的 「流編輯器」 (stream editor)。
在其中，我們可以使用簡短的指令來更改檔案，而非直接編輯檔案內容（雖然我們也可以這樣做）。
指令有很多，但最常用的是 `s`: 替換。
例如，我們可以：

```bash
ssh myserver journalctl
 | grep sshd
 | grep "Disconnected from"
 | sed 's/.*Disconnected from //'
```

<!-- What we just wrote was a simple _regular expression_; a powerful
construct that lets you match text against patterns. The `s` command is
written on the form: `s/REGEX/SUBSTITUTION/`, where `REGEX` is the
regular expression you want to search for, and `SUBSTITUTION` is the
text you want to substitute matching text with. -->
我們剛剛寫了一段 _正規表達式_ ; 正規表達式允許我們匹配符合特定句法的字串。 
`s` 命令的使用方式是這樣： `s/REGEX/SUBSTITUTION/`, 其中 `REGEX` 是我們需要匹配的正規表達式， 
`SUBSTITUTION` 是用於替代匹配結果的字串。

<!-- ## Regular expressions -->
## 正規表達式

<!-- Regular expressions are common and useful enough that it's worthwhile to
take some time to understand how they work. Let's start by looking at
the one we used above: `/.*Disconnected from /`. Regular expressions are
usually (though not always) surrounded by `/`. Most ASCII characters
just carry their normal meaning, but some characters have "special"
matching behavior. Exactly which characters do what vary somewhat
between different implementations of regular expressions, which is a
source of great frustration. Very common patterns are: -->
正規表達式很常見也很有用，值得用些時間去理解它。
讓我們從上面使用過的字串開始: `/.*Disconnected from /`. 
正規表達式通常 (也有例外) 被 `/` 包圍。
多數 ASCII 字元代表其自身的含義，有些則擁有「特別的」含義。
由於正規表達式實現方法的不同，這些符號也常常有不同的含義，這讓我們感到有些挫折。
常見的表達式有：

 <!-- 
 - `.` means "any single character" except newline
 - `*` zero or more of the preceding match
 - `+` one or more of the preceding match
 - `[abc]` any one character of `a`, `b`, and `c`
 - `(RX1|RX2)` either something that matches `RX1` or `RX2`
 - `^` the start of the line
 - `$` the end of the line 
 -->
 - `.` 意為除去換行符外的 「任意一個字元」 
 - `*` 0或多個 `*` 前的字元
 - `+` 1或更多個 `*` 前的字元
 - `[abc]`  `a`, `b`, 與 `c` 中的任意一個
 - `(RX1|RX2)` 能匹配 `RX1` 或 `RX2` 中的任意一個的字串
 - `^` 行首
 - `$` 行尾

<!-- sed's regular expressions are somewhat weird, and will require you to put a \ before most of these to give them their special meaning. Or you can pass -E. -->
`sed` 的正規表達式有些古怪，需要你在這些表達式前使用 `\` 來使它們擁有這些特殊含義。
也可以使用 `-E` 達成同樣效果。

<!-- So, looking back at `/.*Disconnected from /`, we see that it matches
any text that starts with any number of characters, followed by the
literal string "Disconnected from &rdquo;. Which is what we wanted. But
beware, regular expressions are trixy. What if someone tried to log in
with the username "Disconnected from"? We'd have: -->
讓我們重回 `/.*Disconnected from /`, 可以看出它會匹配以任意字元起始，緊接
"Disconnected from" 的字串，這就是我們需要的。
但要小心，正規表達式時而有些棘手。
試想某人嘗試使用 "Disconnected from" 作為使用者名登入,我們將會有:

```
Jan 17 03:13:00 thesquareplanet.com sshd[2631]: Disconnected from invalid user Disconnected from 46.97.239.16 port 55920 [preauth]
```

<!-- What would we end up with? Well, `*` and `+` are, by default, "greedy".
They will match as much text as they can. So, in the above, we'd end up
with just -->
我們會得到怎樣的結果？實際上，`*` 與 `+` 預設是「貪婪的」。它們會匹配所有可能的字元。
所以，上述字串會得出這樣的匹配結果：

```
46.97.239.16 port 55920 [preauth]
```

<!-- Which may not be what we wanted. In some regular expression
implementations, you can just suffix `*` or `+` with a `?` to make them
non-greedy, but sadly `sed` doesn't support that. We _could_ switch to
perl's command-line mode though, which _does_ support that construct: -->
這並不是我們需要的。在一些正規表達式的實現中，我們可以單純給`*` 與 `+` 加入 `?` 字尾使其轉變為非貪婪模式。只可惜，`sed` 並不支援這種方式。
我們 _可以_ 換用 perl 的命令列模式，它 _支援_ 這樣的結構：

```bash
perl -pe 's/.*?Disconnected from //'
```

<!-- We'll stick to `sed` for the rest of this, because it's by far the more
common tool for these kinds of jobs. `sed` can also do other handy
things like print lines following a given match, do multiple
substitutions per invocation, search for things, etc. But we won't cover
that too much here. `sed` is basically an entire topic in and of itself,
but there are often better tools. -->
我們會使用 `sed` 完成後續的工作，因為它是最常見的工具。 
`sed` 也善於完成類似於印出匹配後的字串，一次使用就完成多次替換等工作。
但是我們課中不會講解太多。 
`sed` 比較萬能，但是對於特定細小功能往往有更優秀的工具。

<!-- Okay, so we also have a suffix we'd like to get rid of. How might we do
that? It's a little tricky to match just the text that follows the
username, especially if the username can have spaces and such! What we
need to do is match the _whole_ line: -->
好了，現在我們依然有需要去除的字尾，我們應該如何處理？
僅匹配使用者名後的文字有些困難，當使用者名字含有空格時更是如此！
我們此時應做的就是匹配 _整句話_ ：

```bash
 | sed -E 's/.*Disconnected from (invalid |authenticating )?user .* [^ ]+ port [0-9]+( \[preauth\])?$//'
```

<!-- Let's look at what's going on with a [regex
debugger](https://regex101.com/r/qqbZqh/2). Okay, so the start is still
as before. Then, we're matching any of the "user" variants (there are
two prefixes in the logs). Then we're matching on any string of
characters where the username is. Then we're matching on any single word
(`[^ ]+`; any non-empty sequence of non-space characters). Then the word
"port" followed by a sequence of digits. Then possibly the suffix
`[preauth]`, and then the end of the line. -->
讓我們用[regex debugger](https://regex101.com/r/qqbZqh/2)來看看會得出怎樣的結果。
起始的部分和之前是一樣的。然後，我們匹配出所有型別的 「user」 變體(在日誌中有兩種不同字首)。
接下來我們匹配屬於使用者名稱的所有字元。
再之後，匹配任意一個單詞(`[^ ]+`; 匹配由任意非空格組成的非空字串)。
此時，再匹配 「port」 與接續的一串數字，和可能存在的字尾 `[preauth]`， 直至行尾。

<!-- Notice that with this technique, as username of "Disconnected from"
won't confuse us any more. Can you see why? -->
採用這種方法，即使使用者名是 「Disconnected from」，我們也可以匹配正確內容，你能理解其原理嗎？

<!-- There is one problem with this though, and that is that the entire log
becomes empty. We want to _keep_ the username after all. For this, we
can use "capture groups". Any text matched by a regex surrounded by
parentheses is stored in a numbered capture group. These are available
in the substitution (and in some engines, even in the pattern itself!)
as `\1`, `\2`, `\3`, etc. So: -->
還剩下一個問題需要解決，就是日誌的全部內容都被替換成了空字串。
我們希望能 _留下_ 匹配到的使用者名。
為了達成此目的，我們可以使用 「捕捉群」(capture groups)。
被括號包圍的正規表達式會被按順序存入捕捉群，而捕捉群的內容可以在替換時取用(在有些正規表達式實現中，替換內容甚至支援正規表達式本身！)。
使用 `\1`, `\2`, `\3` 以此類推來取用其內容：

```bash
 | sed -E 's/.*Disconnected from (invalid |authenticating )?user (.*) [^ ]+ port [0-9]+( \[preauth\])?$/\2/'
```

<!-- As you can probably imagine, you can come up with _really_ complicated
regular expressions. For example, here's an article on how you might
match an [e-mail
address](https://www.regular-expressions.info/email.html). It's [not
easy](https://emailregex.com/). And there's [lots of
discussion](https://stackoverflow.com/questions/201323/how-to-validate-an-email-address-using-a-regular-expression/1917982).
And people have [written
tests](https://fightingforalostcause.net/content/misc/2006/compare-email-regex.php).
And [test matrices](https://mathiasbynens.be/demo/url-regex). You can
even write a regex for determining if a given number [is a prime
number](https://www.noulakaz.net/2007/03/18/a-regular-expression-to-check-for-prime-numbers/). -->
你大概可以想象到，我們有時需要 _真的非常_ 複雜的正規表達式。
例如，此處有文章介紹如何匹配[電子郵件](https://www.regular-expressions.info/email.html)，
這實際上很[困難](https://emailregex.com/)。
人們對其進行了[很多討論](https://stackoverflow.com/questions/201323/how-to-validate-an-email-address-using-a-regular-expression/1917982)，
還寫出了許多[測試用例](https://fightingforalostcause.net/content/misc/2006/compare-email-regex.php)，與[test matrices](https://mathiasbynens.be/demo/url-regex)。
我們甚至還能用正規表達式判斷一個數是否為[質數](https://www.noulakaz.net/2007/03/18/a-regular-expression-to-check-for-prime-numbers/)。

<!-- Regular expressions are notoriously hard to get right, but they are also
very handy to have in your toolbox! -->
正規表達式可是相當難以確保正確的，但是他們依然十分好用！

<!-- ## Back to data wrangling -->
## 回到資料處理

<!-- Okay, so we now have -->
現在我們可以寫出：

```bash
ssh myserver journalctl
 | grep sshd
 | grep "Disconnected from"
 | sed -E 's/.*Disconnected from (invalid |authenticating )?user (.*) [^ ]+ port [0-9]+( \[preauth\])?$/\2/'
```

<!-- `sed` can do all sorts of other interesting things, like injecting text
(with the `i` command), explicitly printing lines (with the `p`
command), selecting lines by index, and lots of other things. Check `man
sed`! -->
`sed` 還有能力做到許多有趣的事情，比如注入文字(使用 `i` )，印出指定列(使用 `p` )，或者利用index搜尋等。我們可以使用 `man sed` 檢視更多能做的事情!

<!-- Anyway. What we have now gives us a list of all the usernames that have
attempted to log in. But this is pretty unhelpful. Let's look for common
ones: -->
總之，我們現在已經獲得了由曾經嘗試登入的使用者名組成的表，不過這還用處不大，讓我們看看有哪些人時常嘗試登入：

```bash
ssh myserver journalctl
 | grep sshd
 | grep "Disconnected from"
 | sed -E 's/.*Disconnected from (invalid |authenticating )?user (.*) [^ ]+ port [0-9]+( \[preauth\])?$/\2/'
 | sort | uniq -c
```

<!-- `sort` will, well, sort its input. `uniq -c` will collapse consecutive
lines that are the same into a single line, prefixed with a count of the
number of occurrences. We probably want to sort that too and only keep
the most common logins: -->
`sort` 將會，如同字面意思，對他排序。 `uniq -c` 會合併連續的行並寫出出現次數。
我們希望能按照出現次數排序，來找出最常登入的使用者：

```bash
ssh myserver journalctl
 | grep sshd
 | grep "Disconnected from"
 | sed -E 's/.*Disconnected from (invalid |authenticating )?user (.*) [^ ]+ port [0-9]+( \[preauth\])?$/\2/'
 | sort | uniq -c
 | sort -nk1,1 | tail -n10
```

<!-- `sort -n` will sort in numeric (instead of lexicographic) order. `-k1,1`
means "sort by only the first whitespace-separated column". The `,n`
part says "sort until the `n`th field, where the default is the end of
the line. In this _particular_ example, sorting by the whole line
wouldn't matter, but we're here to learn! -->
`sort -n` 將會按照數字排序 (預設是按照字母順序) 。
 `-k1,1` 表示 「按照被空格分隔的第一行排序」。 
`,n`表示 「僅排序前 `n` 個部分」 預設是醬所有部分都排序。
在這個 _典型的_ 例子中，排序所有部分沒有任何問題，我們只是為了講授 `,n` 的用法才使用了這個！

<!-- If we wanted the _least_ common ones, we could use `head` instead of
`tail`. There's also `sort -r`, which sorts in reverse order. -->
如果我們想找到 _最不常見_ 的登入者，我們可以使用 `head` 替換 `tail`，或者使用 `sort -r`， 它可以反向排序。

<!-- Okay, so that's pretty cool, but we'd sort of like to only give the
usernames, and maybe not one per line? -->
結果已經非常好了，但我們只想要使用者名稱，而且不要一列只寫一個。

```bash
ssh myserver journalctl
 | grep sshd
 | grep "Disconnected from"
 | sed -E 's/.*Disconnected from (invalid |authenticating )?user (.*) [^ ]+ port [0-9]+( \[preauth\])?$/\2/'
 | sort | uniq -c
 | sort -nk1,1 | tail -n10
 | awk '{print $2}' | paste -sd,
```

<!-- Let's start with `paste`: it lets you combine lines (`-s`) by a given
single-character delimiter (`-d`). But what's this `awk` business? -->
`paste` 允許我們指定一個分隔符(`-d`)來合併列(`-s`)。
不過這個 `awk` 是做什麼的？

<!-- ## awk -- another editor -->
## awk -- 另一種編輯器

<!-- `awk` is a programming language that just happens to be really good at
processing text streams. There is _a lot_ to say about `awk` if you were
to learn it properly, but as with many other things here, we'll just go
through the basics. -->
`awk` 是一種非常善於處理文字的程式語言。`awk` 有 _非常多_ 可以學習的功能，不過此課我們只介紹一點基本知識。
　
<!-- First, what does `{print $2}` do? Well, `awk` programs take the form of
an optional pattern plus a block saying what to do if the pattern
matches a given line. The default pattern (which we used above) matches
all lines. Inside the block, `$0` is set to the entire line's contents,
and `$1` through `$n` are set to the `n`th _field_ of that line, when
separated by the `awk` field separator (whitespace by default, change
with `-F`). In this case, we're saying that, for every line, print the
contents of the second field, which happens to be the username! -->
首先，`{print $2}` 有什麼作用？ `awk` 程式可以接受一個選項串和一個指令區塊，指出當匹配到內容時應該做出什麼動作。
在指令碼塊中 `$0` 代表整列的文字， `$1` 到 `$n` 代表此列中第 `n` 個 _欄位_ ，
這些 _欄位_ 由 `awk` 域分隔符劃分(預設是空格， 可以用過 `-F` 指定)。
在這個例子中，這些程式碼的意思是 印出每列第二個部分，也就是使用者名！

<!-- Let's see if we can do something fancier. Let's compute the number of
single-use usernames that start with `c` and end with `e`: -->
讓我們看看還能做到什麼奇異功能。讓我們找出所有以 `c` 起始， 以 `e` 結尾，且只嘗試過一次登入的人:

```bash
 | awk '$1 == 1 && $2 ~ /^c[^ ]*e$/ { print $2 }' | wc -l
```

<!-- There's a lot to unpack here. First, notice that we now have a pattern
(the stuff that goes before `{...}`). The pattern says that the first
field of the line should be equal to 1 (that's the count from `uniq
-c`), and that the second field should match the given regular
expression. And the block just says to print the username. We then count
the number of lines in the output with `wc -l`. -->
這裡有許多要講解。首先，請注意我們指定了表達式(也就是 `{...}` 前面的那些)。
這個表達式要求此列文字的第一部分必須是 1 (這部分是 `uniq -c` 取得的)，
並且第二部分必須匹配給定的表達式。
指令碼塊中的內容則表示印出使用者名。
接下來我們使用 `wc -l` 統計輸出結果的列數。

<!-- However, `awk` is a programming language, remember? -->
別忘記 `awk` 是一種程式語言：

```awk
BEGIN { rows = 0 }
$1 == 1 && $2 ~ /^c[^ ]*e$/ { rows += $1 }
END { print rows }
```

<!-- `BEGIN` is a pattern that matches the start of the input (and `END`
matches the end). Now, the per-line block just adds the count from the
first field (although it'll always be 1 in this case), and then we print
it out at the end. In fact, we _could_ get rid of `grep` and `sed`
entirely, because `awk` [can do it
all](https://backreference.org/2010/02/10/idiomatic-awk/), but we'll
leave that as an exercise to the reader. -->
`BEGIN` 是一種匹配起始輸入 (and `END` 匹配結尾)的模式。
然後，對每列第一部分相加（本例中此部分均為 1 ），
再印出最後結果。
實際上，_無需_ `grep` 與 `sed`，因為 `awk` [可以解決所有問題](https://backreference.org/2010/02/10/idiomatic-awk/)。
至於如何做到，可以參考課後練習。

<!-- ## Analyzing data -->
## 分析數據

<!-- You can do math! For example, add the numbers on each line together: -->
我們也可以做些計算！例如，將每列數字相加：

```bash
 | paste -sd+ | bc -l
```

<!-- Or produce more elaborate expressions: -->
也可以使用更加複雜的方法：

```bash
echo "2*($(data | paste -sd+))" | bc -l
```

<!-- You can get stats in a variety of ways.
[`st`](https://github.com/nferraz/st) is pretty neat, but if you already
have R: -->
我們可以透過多種方式獲取統計數據。
[`st`](https://github.com/nferraz/st) 是個不錯的選擇，不過如果你已經安裝了R：

```bash
ssh myserver journalctl
 | grep sshd
 | grep "Disconnected from"
 | sed -E 's/.*Disconnected from (invalid |authenticating )?user (.*) [^ ]+ port [0-9]+( \[preauth\])?$/\2/'
 | sort | uniq -c
 | awk '{print $1}' | R --slave -e 'x <- scan(file="stdin", quiet=TRUE); summary(x)'
```

<!-- R is another (weird) programming language that's great at data analysis
and [plotting](https://ggplot2.tidyverse.org/). We won't go into too
much detail, but suffice to say that `summary` prints summary statistics
about a matrix, and we computed a matrix from the input stream of
numbers, so R gives us the statistics we wanted! -->
R 是一種 (古怪的) 程式語言，適合用來進行資料分析或[繪圖](https://ggplot2.tidyverse.org/)。
我們不會介紹太多，只需知道 `summary` 會印出關於矩陣的統計結果。
我們從輸入的數字中計算出一個矩陣，R會給出分析結果。

<!-- If you just want some simple plotting, `gnuplot` is your friend: -->
我們可以輕鬆藉助 `gnuplot` 來繪製圖表：

```bash
ssh myserver journalctl
 | grep sshd
 | grep "Disconnected from"
 | sed -E 's/.*Disconnected from (invalid |authenticating )?user (.*) [^ ]+ port [0-9]+( \[preauth\])?$/\2/'
 | sort | uniq -c
 | sort -nk1,1 | tail -n10
 | gnuplot -p -e 'set boxwidth 0.5; plot "-" using 1:xtic(2) with boxes'
```

<!-- ## Data wrangling to make arguments -->
## 使用資料處理來獲取參數

<!-- Sometimes you want to do data wrangling to find things to install or
remove based on some longer list. The data wrangling we've talked about
so far + `xargs` can be a powerful combo: -->
有時我們需要利用資處理來從一個長表中找出想要安裝或反安裝的東西，
利用我們之前講授的知識配合 `xargs` 可以輕鬆實現：

```bash
rustup toolchain list | grep nightly | grep -vE "nightly-x86" | sed 's/-x86.*//' | xargs rustup toolchain uninstall
```

<!-- ## Wrangling binary data -->
## 處理二進位

<!-- So far, we have mostly talked about wrangling textual data, but pipes
are just as useful for binary data. For example, we can use ffmpeg to
capture an image from our camera, convert it to grayscale, compress it,
send it to a remote machine over SSH, decompress it there, make a copy,
and then display it. -->
雖然截至目前我們都在處理文字，不過管道對於處理二進位資料也十分有效。
例如，我們可以使用ffmpeg來從相機中獲取影象，轉換為灰度，再進行壓縮，最後通過SSH傳送至遠端，在遠端解壓縮後存檔並顯示。

```bash
ffmpeg -loglevel panic -i /dev/video0 -frames 1 -f image2 -
 | convert - -colorspace gray -
 | gzip
 | ssh mymachine 'gzip -d | tee copy.jpg | env DISPLAY=:0 feh -'
```

# 課後練習

1. 學習這篇 [簡短的交互式正規表達式教學](https://regexone.com/)。
2. 統計words文件 (`/usr/share/dict/words`) 中包含至少三個`a` 且不以`'s` 結尾的單字個數。這些單詞中，出現頻率最高的末尾兩個字母是什麼？ `sed` 的 `y` 命令，或者 `tr` 指令也許可以幫你解決大小寫的問題。共存在多少種詞尾兩字母組合？更進階的問題：哪個組合從未出現過？
3. 進行原地替換似乎很實用，例如： `sed s/REGEX/SUBSTITUTION/ input.txt > input.txt`。但是這並不是一個明智的做法，為什麼呢？還是說只有 `sed` 是這樣的? 查看 `man sed` 來回答這個問題。
4. 找出您最近十次開機的開機時間平均數、中位數和最長時間。在Linux上需要用到 `journalctl` ，而在 macOS 上使用 `log show`。找到每次起到開始和結束時的時間戳記。在Linux上類似這樣操作：
   ```
   Logs begin at ...
   ```
   和
   ```
   systemd[577]: Startup finished in ...
   ```
   在 macOS, [查找](https://eclecticlight.co/2018/03/21/macos-unified-log-3-finding-your-way/):
   ```
   === system boot:
   ```
   和
   ```
   Previous shutdown cause: 5
   ```
5. 查看之前三次重新啟動訊息中不同的部分 (參考 `journalctl` 的 `-b` 選項)。將這一任務分為幾個步驟，首先獲取之前三次啟動的日誌，也許獲取啟動日誌的命令就有合適的選項可以幫助您提取前三次啟動的日誌，亦或者您可以使用 `sed '0,/ STRING/d'` 來刪除 `STRING` 匹配到的字符串前面的全部內容。然後，過濾掉每次都不相同的部分，例如時間戳記。下一步，重複記錄輸入行並對其計數(可以使用 `uniq` )。最後，刪除所有出現過3次的內容（因為這些內容為重複的部分）。
6. 在網路上找一個類似 [這個](https://stats.wikimedia.org/EN/TablesWikipediaZZ.htm)、[這個](https://ucr.fbi.gov/crime-in-the-u.s/2016/crime-in-the-u.s.-2016/topic-pages/tables/table-1)或[這個](https://www.springboard.com/blog/free-public-data-sets-data-science-project/)的資料表格。用 `curl` 獲取表格並提取其中兩行數據，如果您想要獲取的是HTML資料，那麼 `pup` 可能會更有幫助。對於JSON類型的數據，可以試試 `jq` 。請使用一條指令來找出其中一行的最大值和最小值，用另外一條指令計算兩行之間差的總和。 