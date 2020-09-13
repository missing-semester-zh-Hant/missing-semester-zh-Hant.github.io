---
layout: lecture
title: "Security and Cryptography （尚未翻譯）"
date: 2019-01-28
ready: true
video:
  aspect: 56.25
  id: tjwobAmnKTo
---

<!-- Last year's [security and privacy lecture](/2019/security/) focused on how you
can be more secure as a computer _user_. This year, we will focus on security
and cryptography concepts that are relevant in understanding tools covered
earlier in this class, such as the use of hash functions in Git or key
derivation functions and symmetric/asymmetric cryptosystems in SSH. -->
去年的[安全和隱私講座](/2019/security/)重點討論瞭如何以電腦 _使用者_ 的身份提高安全性。
今年，我們將重點關注與本課程前面介紹的工具有關的，在安全性和密碼學概念方面的知識。
例如，在 Git 中使用 hash 函數或在 SSH 中使用密鑰派生函數和對稱/非對稱密碼系統。

<!-- This lecture is not a substitute for a more rigorous and complete course on
computer systems security ([6.858](https://css.csail.mit.edu/6.858/)) or
cryptography ([6.857](https://courses.csail.mit.edu/6.857/) and 6.875). Don't
do security work without formal training in security. Unless you're an expert,
don't [roll your own
crypto](https://www.schneier.com/blog/archives/2015/05/amateurs_produc.html).
The same principle applies to systems security. -->
此課程不可作爲計算機系統安全([6.858](https://css.csail.mit.edu/6.858/)) 或
密碼學 ([6.857](https://courses.csail.mit.edu/6.857/) 與 6.875)的替代。
不要在沒有接受良好安全性教學的情況下從事相關工作。
除非你是專家，不要[自創加密方法](https://www.schneier.com/blog/archives/2015/05/amateurs_produc.html)，
這在系統安全方面是同樣的。

<!-- This lecture has a very informal (but we think practical) treatment of basic
cryptography concepts. This lecture won't be enough to teach you how to
_design_ secure systems or cryptographic protocols, but we hope it will be
enough to give you a general understanding of the programs and protocols you
already use. -->
這節課對基本密碼學概念有着非常簡略（但我們認爲很實用）的介紹。
若你想 _設計_ 安全系統或加密協議，僅僅學習本課是不夠的。
但我們希望此課可以讓你大致瞭解已經廣泛使用的程式與協議。

<!-- # Entropy -->
# 熵(Entropy)

<!-- [Entropy](https://en.wikipedia.org/wiki/Entropy_(information_theory)) is a
measure of randomness. This is useful, for example, when determining the
strength of a password. -->
[熵](https://en.wikipedia.org/wiki/Entropy_(information_theory))是對隨機性的一種度量。
在例如測試密碼強度等環境下，熵有着重要作用。

<!-- ![XKCD 936: Password Strength](https://imgs.xkcd.com/comics/password_strength.png) -->
![XKCD 936: 密碼安全性](https://xkcd.tw/strip/936.png)

<!-- As the above [XKCD comic](https://xkcd.com/936/) illustrates, a password like
"correcthorsebatterystaple" is more secure than one like "Tr0ub4dor&3". But how
do you quantify something like this? -->
如同上面 [XKCD 漫畫](https://xkcd.tw/936) 描繪的，
"correcthorsebatterystaple" 比 "Tr0ub4dor&3" 更爲安全。
我們該如何判斷安全性？

<!-- Entropy is measured in _bits_, and when selecting uniformly at random from a
set of possible outcomes, the entropy is equal to `log_2(# of possibilities)`.
A fair coin flip gives 1 bit of entropy. A dice roll (of a 6-sided die) has
\~2.58 bits of entropy. -->
熵以 _位元_ 度量，對於一個均勻分佈的隨機變數，熵等於 `log_2(可能性總數)`。
擲一次硬幣的uhti 1 位元，擲一次（六面）骰子的熵約爲 2.58 位元。

<!-- You should consider that the attacker knows the _model_ of the password, but
not the randomness (e.g. from [dice
rolls](https://en.wikipedia.org/wiki/Diceware)) used to select a particular
password. -->
我們通常認爲攻擊者瞭解密碼的 _模型_， 但是不知道密碼的隨機性（例如 [擲骰子](https://en.wikipedia.org/wiki/Diceware)）。

<!-- How many bits of entropy is enough? It depends on your threat model. For online
guessing, as the XKCD comic points out, \~40 bits of entropy is pretty good. To
be resistant to offline guessing, a stronger password would be necessary (e.g.
80 bits, or more). -->
多少位元的熵是足夠安全的？這通常取決於威脅模型。
上面的 XKCD 漫畫指出，對於線上密碼攻擊，大約 40 位元的熵即可。
對於線下攻擊，需要 80 位元或更強的密碼。

<!-- # Hash functions -->
# 密碼雜湊函式

<!-- A [cryptographic hash
function](https://en.wikipedia.org/wiki/Cryptographic_hash_function) maps data
of arbitrary size to a fixed size, and has some special properties. A rough
specification of a hash function is as follows: -->
[密碼雜湊函式](https://en.wikipedia.org/wiki/Cryptographic_hash_function)
會將任意大小的資料映射至指定大小。
一個粗略的雜湊函式規範大概是這樣的：

```
hash(value: array<byte>) -> vector<byte, N>  (N 是固定數目)
```

<!-- An example of a hash function is [SHA1](https://en.wikipedia.org/wiki/SHA-1),
which is used in Git. It maps arbitrary-sized inputs to 160-bit outputs (which
can be represented as 40 hexadecimal characters). We can try out the SHA1 hash
on an input using the `sha1sum` command: -->
一個雜湊函式的例子是 Git 使用的 [SHA1](https://en.wikipedia.org/wiki/SHA-1)。
它會將任意大小的輸入映射至 160 位元（即 40 個十六進位字元）中。
我們可以使用 `sha1sum` 指令：

```console
$ printf 'hello' | sha1sum
aaf4c61ddcc5e8a2dabede0f3b482cd9aea9434d
$ printf 'hello' | sha1sum
aaf4c61ddcc5e8a2dabede0f3b482cd9aea9434d
$ printf 'Hello' | sha1sum 
f7ff9e8b7bb2e09b70935a5d785e0cc5d9d0abf0
```

<!-- At a high level, a hash function can be thought of as a hard-to-invert
random-looking (but deterministic) function (and this is the [ideal model of a
hash function](https://en.wikipedia.org/wiki/Random_oracle)). A hash function
has the following properties: -->
更通用的解釋是，雜湊函式可以被認爲是不可逆且隨機（但是具有特定性）的函式（這是 [雜湊函式的理想模型](https://en.wikipedia.org/wiki/Random_oracle)）。
一個雜湊函式將會有如下性質：

<!-- - Deterministic: the same input always generates the same output.
- Non-invertible: it is hard to find an input `m` such that `hash(m) = h` for
some desired output `h`.
- Target collision resistant: given an input `m_1`, it's hard to find a
different input `m_2` such that `hash(m_1) = hash(m_2)`.
- Collision resistant: it's hard to find two inputs `m_1` and `m_2` such that
`hash(m_1) = hash(m_2)` (note that this is a strictly stronger property than
target collision resistance). -->
- 確定性: 對於相同輸入，輸出也是相同的
- 不可逆: 對於函式 `hash(m) = h`，給出 `h` 幾乎不可能求得對應的 `m`。
- 弱抵抗/低碰撞抗性: 給予輸入 `m_1`, 很難找到 `m_2` 使得 `hash(m_1) = hash(m_2)`。
- 強抵抗/高碰撞抗性: 很難找到兩個輸入 `m_1` 和 `m_2` 使得 `hash(m_1) = hash(m_2)`。注意這比弱抵抗更爲嚴格。

<!-- Note: while it may work for certain purposes, SHA-1 is [no
longer](https://shattered.io/) considered a strong cryptographic hash function.
You might find this table of [lifetimes of cryptographic hash
functions](https://valerieaurora.org/hash.html) interesting. However, note that
recommending specific hash functions is beyond the scope of this lecture. If you
are doing work where this matters, you need formal training in
security/cryptography. -->
注意：雖然對於一般用途還有效，SHA-1 已經[不再](https://shattered.io/)被認爲是強密碼雜湊函式了。
你也許會對[密碼雜湊函式的生命週期](https://valerieaurora.org/hash.html)感興趣。
對於特定需求推薦指定雜湊函式不在此課程的涵蓋範圍內，如果你有這些需求，需要學習安全學或密碼學。

<!-- ## Applications -->
## 運用

<!-- - Git, for content-addressed storage. The idea of a [hash
function](https://en.wikipedia.org/wiki/Hash_function) is a more general
concept (there are non-cryptographic hash functions). Why does Git use a
cryptographic hash function?
- A short summary of the contents of a file. Software can often be downloaded
from (potentially less trustworthy) mirrors, e.g. Linux ISOs, and it would be
nice to not have to trust them. The official sites usually post hashes
alongside the download links (that point to third-party mirrors), so that the
hash can be checked after downloading a file.
- [Commitment schemes](https://en.wikipedia.org/wiki/Commitment_scheme).
Suppose you want to commit to a particular value, but reveal the value itself
later. For example, I want to do a fair coin toss "in my head", without a
trusted shared coin that two parties can see. I could choose a value `r =
random()`, and then share `h = sha256(r)`. Then, you could call heads or tails
(we'll agree that even `r` means heads, and odd `r` means tails). After you
call, I can reveal my value `r`, and you can confirm that I haven't cheated by
checking `sha256(r)` matches the hash I shared earlier. -->
- Git，用於內容尋址存儲。[雜湊函式](https://en.wikipedia.org/wiki/Hash_function)
是一個寬泛的概念（存在與密碼學無關的雜湊函式）。爲什麼 Git 要使用它？
- 檔案資料摘要。例如 Linux ISO 這種軟體經常需要從（潛在不可信的）非官方鏡像下載，此時不信任它們比較好。
官方站點通常會在鏡像站點旁邊列出 hash，這樣你可以在下載後檢驗得到的文件。
- [承諾方案(Commitment scheme)](https://en.wikipedia.org/wiki/Commitment_scheme).
假定你需要承諾一個值，但要之後揭示它。例如，我要“在想象中”擲一次硬幣，但是這個硬幣對你不可見也不受信任。
我可以選擇一個值 `r = random()`，然後告訴你 `h = sha256(r)`。我們同意 `r` 爲偶數時代表正面，奇數時代表反面。
之後，你可以選擇正面還是反面。判斷勝負後，我可以告訴你我的值 `r`，並且可以通過檢驗 `sha256(r)`
 來確認我沒有作弊。

<!-- # Key derivation functions -->
# 密鑰衍生函式

<!-- A related concept to cryptographic hashes, [key derivation
functions](https://en.wikipedia.org/wiki/Key_derivation_function) (KDFs) are
used for a number of applications, including producing fixed-length output for
use as keys in other cryptographic algorithms. Usually, KDFs are deliberately
slow, in order to slow down offline brute-force attacks. -->
[密鑰衍生函式](https://en.wikipedia.org/wiki/Key_derivation_function) (KDFs) 
作爲密碼雜湊函式的相關概念，被運用於多個方面，包括生成可以在其他密碼算法中使用的固定長度密鑰等。
爲了對抗線下的窮舉攻擊，KDFs 通常故意被設計成緩慢運行。

<!-- ## Applications -->
## 運用

<!-- - Producing keys from passphrases for use in other cryptographic algorithms
(e.g. symmetric cryptography, see below).
- Storing login credentials. Storing plaintext passwords is bad; the right
approach is to generate and store a random
[salt](https://en.wikipedia.org/wiki/Salt_(cryptography)) `salt = random()` for
each user, store `KDF(password + salt)`, and verify login attempts by
re-computing the KDF given the entered password and the stored salt. -->
- 生成可以在其他加密算法中使用的密鑰。(例如對稱加密，下面會講解)。
- 存儲登入憑據時，儲存明文密碼是不適宜的。正確的方法是對每個使用者生成並儲存
[鹽](https://en.wikipedia.org/wiki/Salt_(cryptography)) `salt = random()`，
以及 `KDF(password + salt)`，並在每次登入後檢驗它。

# 對稱加密

<!-- Hiding message contents is probably the first concept you think about when you
think about cryptography. Symmetric cryptography accomplishes this with the
following set of functionality: -->
當你需要加密時，最先想到的是隱藏明文信息，對稱加密使用以下幾種方法來實現此功能：

<!-- ```
keygen() -> key  (this function is randomized)

encrypt(plaintext: array<byte>, key) -> array<byte>  (the ciphertext)
decrypt(ciphertext: array<byte>, key) -> array<byte>  (the plaintext)
``` -->
```
keygen() -> key  (這是一個隨機方法)

encrypt(plaintext: array<byte>, key) -> array<byte>  (獲得密文)
decrypt(ciphertext: array<byte>, key) -> array<byte>  (獲得明文)
```

<!-- The encrypt function has the property that given the output (ciphertext), it's
hard to determine the input (plaintext) without the key. The decrypt function
has the obvious correctness property, that `decrypt(encrypt(m, k), k) = m`. -->
透過加密方法獲得的輸出(密文)很難在不知道 key 的情況下解得其輸入(明文)。
解密方法具有明顯的正確性，即 `decrypt(encrypt(m, k), k) = m` 在提供正確資料時一定成立。

<!-- An example of a symmetric cryptosystem in wide use today is
[AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard). -->
一個現在被廣泛使用的對稱加密例子是
[AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)。

<!-- ## Applications -->
## 運用

<!-- - Encrypting files for storage in an untrusted cloud service. This can be
combined with KDFs, so you can encrypt a file with a passphrase. Generate `key
= KDF(passphrase)`, and then store `encrypt(file, key)`. -->
- 在不信任的雲服務上加密文件。可以與密碼雜湊函式共同使用。
透過 `key = KDF(passphrase)` 生成一個密鑰，然後在雲上儲存  `encrypt(file, key)`。

<!-- # Asymmetric cryptography -->
# 非對稱加密

<!-- The term "asymmetric" refers to there being two keys, with two different roles.
A private key, as its name implies, is meant to be kept private, while the
public key can be publicly shared and it won't affect security (unlike sharing
the key in a symmetric cryptosystem). Asymmetric cryptosystems provide the
following set of functionality, to encrypt/decrypt and to sign/verify: -->
“非對稱”是指存在兩個持有不同職責的密鑰。 
顧名思義，私鑰旨在保持私密，而公鑰可以公開共享，且不會影響安全性（與在對稱加密系統中共享密鑰不同）。
非對稱密碼系統提供以下功能，以進行加密/解密和簽章/檢驗：

<!-- ```
keygen() -> (public key, private key)  (this function is randomized)

encrypt(plaintext: array<byte>, public key) -> array<byte>  (the ciphertext)
decrypt(ciphertext: array<byte>, private key) -> array<byte>  (the plaintext)

sign(message: array<byte>, private key) -> array<byte>  (the signature)
verify(message: array<byte>, signature: array<byte>, public key) -> bool  (whether or not the signature is valid)

``` -->
```
keygen() -> (public key, private key)  (這是一個隨機方法)

encrypt(plaintext: array<byte>, public key) -> array<byte>  (獲得密文)
decrypt(ciphertext: array<byte>, private key) -> array<byte>  (獲得明文)

sign(message: array<byte>, private key) -> array<byte>  (獲得簽章)
verify(message: array<byte>, signature: array<byte>, public key) -> bool  (檢驗此簽章爲有效的)
```

<!-- The encrypt/decrypt functions have properties similar to their analogs from
symmetric cryptosystems. A message can be encrypted using the _public_ key.
Given the output (ciphertext), it's hard to determine the input (plaintext)
without the _private_ key. The decrypt function has the obvious correctness
property, that `decrypt(encrypt(m, public key), private key) = m`. -->
加密/解密功能類似於對稱加密系統的性質。
可以使用 _公共_ 密鑰對消息進行加密。
給定輸出（密文），沒有 _私有_ 密鑰就很難確定輸入（明文）。
解密函數具有明顯的正確性，即 `decrypt（encrypt（m，public key），private key）= m` 在提供正確資料時一定成立。

<!-- Symmetric and asymmetric encryption can be compared to physical locks. A
symmetric cryptosystem is like a door lock: anyone with the key can lock and
unlock it. Asymmetric encryption is like a padlock with a key. You could give
the unlocked lock to someone (the public key), they could put a message in a
box and then put the lock on, and after that, only you could open the lock
because you kept the key (the private key). -->
對稱和非對稱加密與生活中的鎖類似。
對稱加密系統就像門鎖：任何擁有鑰匙的人都可以鎖定和解鎖。
非對稱加密就像帶鑰匙的掛鎖。 您可以將鎖本身（公鑰）提供給某人，他們可以在盒子內放入消息，然後鎖上它。
只有你可以打開該鎖，因為你持有鎖的鑰匙（私鑰）。

<!-- The sign/verify functions have the same properties that you would hope physical
signatures would have, in that it's hard to forge a signature. No matter the
message, without the _private_ key, it's hard to produce a signature such that
`verify(message, signature, public key)` returns true. And of course, the
verify function has the obvious correctness property that `verify(message,
sign(message, private key), public key) = true`. -->
簽章/檢驗功能與書面簽名類似，即難以僞造。
在不知道 _私鑰_ 的情況下，很難得出一個可以通過 `verify(message, signature, public key)` 檢驗的簽章。
對於使用私鑰簽章的資料，`verify(message, sign(message, private key), public key) = true` 這種檢驗方式在提供正確資料時一定成立。

<!-- ## Applications -->
## 運用

<!-- - [PGP email encryption](https://en.wikipedia.org/wiki/Pretty_Good_Privacy).
People can have their public keys posted online (e.g. in a PGP keyserver, or on
[Keybase](https://keybase.io/)). Anyone can send them encrypted email.
- Private messaging. Apps like [Signal](https://signal.org/) and
[Keybase](https://keybase.io/) use asymmetric keys to establish private
communication channels.
- Signing software. Git can have GPG-signed commits and tags. With a posted
public key, anyone can verify the authenticity of downloaded software. -->
- [PGP 電郵加密](https://en.wikipedia.org/wiki/Pretty_Good_Privacy).
使用者可以在網路上發佈公鑰 (例如一個 PGP 密鑰伺服器，或者
[Keybase](https://keybase.io/))。任何人都可以向他們發送加密電郵。
- 私密通訊。 類似 [Signal](https://signal.org/) 與
[Keybase](https://keybase.io/) 這樣的程式使用非對稱加密系統來建立私密通訊。
- 程式簽章。Git 支援帶有 GPG 簽章的提交。透過開發人員發佈的公鑰，任何人都可以驗證下載到的軟體。

<!-- ## Key distribution -->
## 密鑰分配

<!-- Asymmetric-key cryptography is wonderful, but it has a big challenge of
distributing public keys / mapping public keys to real-world identities. There
are many solutions to this problem. Signal has one simple solution: trust on
first use, and support out-of-band public key exchange (you verify your
friends' "safety numbers" in person). PGP has a different solution, which is
[web of trust](https://en.wikipedia.org/wiki/Web_of_trust). Keybase has yet
another solution of [social
proof](https://keybase.io/blog/chat-apps-softer-than-tofu) (along with other
neat ideas). Each model has its merits; we (the instructors) like Keybase's
model. -->
非對稱加密很棒，但是在分配公鑰/將公鑰對應至現實中的個體時面臨著很大的挑戰。 
有許多解決此問題的方法。 
Signal 提供了簡單地解決方案：使用者第一次使用時信任其身份，並支持線下的公鑰交換（你需要親自驗證朋友是否安全）。
PGP有另一種解決方案，即[信任網](https://en.wikipedia.org/wiki/Web_of_trust)。 
Keybase 主要使用[社交證明](https://keybase.io/blog/chat-apps-softer-than-tofu)，與一些其他精妙方法。
每一種模型都有其優勢，我們（講師）喜愛 Keybase 的模型。

<!-- # Case studies -->
# 案例

<!-- ## Password managers -->
密碼管理器

<!-- This is an essential tool that everyone should try to use (e.g.
[KeePassXC](https://keepassxc.org/)). Password managers let you use unique,
randomly generated high-entropy passwords for all your websites, and they save
all your passwords in one place, encrypted with a symmetric cipher with a key
produced from a passphrase using a KDF. -->
任何人都應該試一試密碼管理器（例如[KeePassXC](https://keepassxc.org/)）。
密碼管理器會幫助你對每個網站生成隨機且高熵的密碼。它會將你的所有密碼儲存至一處，且透過 KDF 利用你的主密碼生成的密鑰進行對稱加密。

<!-- Using a password manager lets you avoid password reuse (so you're less impacted
when websites get compromised), use high-entropy passwords (so you're less likely to
get compromised), and only need to remember a single high-entropy password. -->
密碼管理器會讓你避免復用密碼（所以當網站數據洩漏時你受到影響較少），且使用高熵密碼（使你自己更難以洩漏密碼）。
密碼管理器只需要你記住一個高熵的主密碼。

<!-- ## Two-factor authentication -->
## 雙重驗證

<!-- [Two-factor
authentication](https://en.wikipedia.org/wiki/Multi-factor_authentication)
(2FA) requires you to use a passphrase ("something you know") along with a 2FA
authenticator (like a [YubiKey](https://www.yubico.com/), "something you have")
in order to protect against stolen passwords and
[phishing](https://en.wikipedia.org/wiki/Phishing) attacks. -->
[雙重驗證](https://en.wikipedia.org/wiki/Multi-factor_authentication) (2FA)
要求你同時使用密碼（“你知道的資料”）和一個 2FA 驗證器（比如[YubiKey](https://www.yubico.com/)，“你擁有的東西”）
來對抗密碼洩漏與[釣魚攻擊](https://en.wikipedia.org/wiki/Phishing)。

<!-- ## Full disk encryption -->
## 全磁碟加密

<!-- Keeping your laptop's entire disk encrypted is an easy way to protect your data
in the case that your laptop is stolen. You can use [cryptsetup +
LUKS](https://wiki.archlinux.org/index.php/Dm-crypt/Encrypting_a_non-root_file_system)
on Linux,
[BitLocker](https://fossbytes.com/enable-full-disk-encryption-windows-10/) on
Windows, or [FileVault](https://support.apple.com/en-us/HT204837) on macOS.
This encrypts the entire disk with a symmetric cipher, with a key protected by
a passphrase. -->
將筆電全磁碟加密是在其被盜時保護資料的簡單辦法。
Linux 系統下使用 [cryptsetup + LUKS](https://wiki.archlinux.org/index.php/Dm-crypt/Encrypting_a_non-root_file_system)，
Windows 下使用 [BitLocker](https://fossbytes.com/enable-full-disk-encryption-windows-10/)，
macOS 下使用 [FileVault](https://support.apple.com/en-us/HT204837)。
它們會通過對稱加密來保護整個硬碟。

<!-- ## Private messaging -->
## 私密通訊

<!-- Use [Signal](https://signal.org/) or [Keybase](https://keybase.io/). End-to-end
security is bootstrapped from asymmetric-key encryption. Obtaining your
contacts' public keys is the critical step here. If you want good security, you
need to authenticate public keys out-of-band (with Signal or Keybase), or trust
social proofs (with Keybase). -->
使用 [Signal](https://signal.org/) 或 [Keybase](https://keybase.io/)。 
非對稱密鑰加密會提升端到端安全性。獲取聯繫人的公鑰是此處的關鍵步驟。
如果想要良好的安全性，需要線下（通過 Signal 或 Keybase ）對公鑰進行身份驗證，或者信任社交證明（透過 Keybase）。

## SSH

<!-- We've covered the use of SSH and SSH keys in an [earlier
lecture](/2020/command-line/#remote-machines). Let's look at the cryptography
aspects of this. -->
[之前的講座](/2020/command-line/#remote-machines)涉及了 SSH 與 SSH 密鑰的使用
讓我們從密碼學方面看看他們。

<!-- When you run `ssh-keygen`, it generates an asymmetric keypair, `public_key,
private_key`. This is generated randomly, using entropy provided by the
operating system (collected from hardware events, etc.). The public key is
stored as-is (it's public, so keeping it a secret is not important), but at
rest, the private key should be encrypted on disk. The `ssh-keygen` program
prompts the user for a passphrase, and this is fed through a key derivation
function to produce a key, which is then used to encrypt the private key with a
symmetric cipher. -->
當你執行 `ssh-keygen`時候，它會給出一組非對稱密鑰，即`public_key, private_key`。
這使用作業系統（從硬體事件擷取的）熵隨機生成。
公鑰會被明文存儲（它是公開的，所以保密性不重要），不過私鑰必須加密。
`ssh-keygen` 程式會要求使用者輸入一個密碼，並透過密鑰生成函式來使用該密碼生成一個密鑰，`ssh-keygen` 會來利用此密鑰透過對稱加密加密私鑰。

<!-- In use, once the server knows the client's public key (stored in the
`.ssh/authorized_keys` file), a connecting client can prove its identity using
asymmetric signatures. This is done through
[challenge-response](https://en.wikipedia.org/wiki/Challenge%E2%80%93response_authentication).
At a high level, the server picks a random number and sends it to the client.
The client then signs this message and sends the signature back to the server,
which checks the signature against the public key on record. This effectively
proves that the client is in possession of the private key corresponding to the
public key that's in the server's `.ssh/authorized_keys` file, so the server
can allow the client to log in. -->
在現實場景中，當伺服器已知用戶的公鑰（存儲在 `.ssh/authorized_keys`），與其鏈接的客戶端可以透過非對稱簽章證明其身份。
這個過程由[質詢-應答機制](https://en.wikipedia.org/wiki/Challenge%E2%80%93response_authentication)完成。
通常來說，伺服器選擇一個隨機數字，並將其傳送至客戶端。客戶端使用私鑰對此數字簽章後返回伺服器。
伺服器隨後使用 `.ssh/authorized_keys` 中儲存的用戶公鑰來驗證此返回信息是否由對應的私鑰簽章。
如果正確，伺服器便可放行客戶端登入。

{% comment %}
extra topics, if there's time

security concepts, tips
- biometrics
- HTTPS
{% endcomment %}

<!-- # Resources -->
# 資源

<!-- - [Last year's notes](/2019/security/): from when this lecture was more focused on security and privacy as a computer user
- [Cryptographic Right Answers](https://latacora.micro.blog/2018/04/03/cryptographic-right-answers.html): answers "what crypto should I use for X?" for many common X。-->
- [去年的講座](/2019/security/): 更注重於使用者如何增強隱私保護並保證安全
- [Cryptographic Right Answers](https://latacora.micro.blog/2018/04/03/cryptographic-right-answers.html): 解答了許多常見場景下的 “對於此場景，我該如何選擇加密方式” 的問題


<!-- # Exercises -->
# 課後練習

<!-- 1. **Entropy.**
    1. Suppose a password is chosen as a concatenation of five lower-case
       dictionary words, where each word is selected uniformly at random from a
       dictionary of size 100,000. An example of such a password is
       `correcthorsebatterystaple`. How many bits of entropy does this have?
    1. Consider an alternative scheme where a password is chosen as a sequence
       of 8 random alphanumeric characters (including both lower-case and
       upper-case letters). An example is `rg8Ql34g`. How many bits of entropy
       does this have?
    1. Which is the stronger password?
    1. Suppose an attacker can try guessing 10,000 passwords per second. On
       average, how long will it take to break each of the passwords?
1. **Cryptographic hash functions.** Download a Debian image from a
   [mirror](https://www.debian.org/CD/http-ftp/) (e.g. [from this Argentinean
   mirror](http://debian.xfree.com.ar/debian-cd/current/amd64/iso-cd/).
   Cross-check the hash (e.g. using the `sha256sum` command) with the hash
   retrieved from the official Debian site (e.g. [this
   file](https://cdimage.debian.org/debian-cd/current/amd64/iso-cd/SHA256SUMS)
   hosted at `debian.org`, if you've downloaded the linked file from the
   Argentinean mirror).
1. **Symmetric cryptography.** Encrypt a file with AES encryption, using
   [OpenSSL](https://www.openssl.org/): `openssl aes-256-cbc -salt -in {input
   filename} -out {output filename}`. Look at the contents using `cat` or
   `hexdump`. Decrypt it with `openssl aes-256-cbc -d -in {input filename} -out
   {output filename}` and confirm that the contents match the original using
   `cmp`.
1. **Asymmetric cryptography.**
    1. Set up [SSH
       keys](https://www.digitalocean.com/community/tutorials/how-to-set-up-ssh-keys--2)
       on a computer you have access to (not Athena, because Kerberos interacts
       weirdly with SSH keys). Rather than using RSA keys as in the linked
       tutorial, use more secure [ED25519
       keys](https://wiki.archlinux.org/index.php/SSH_keys#Ed25519). Make sure
       your private key is encrypted with a passphrase, so it is protected at
       rest.
    1. [Set up GPG](https://www.digitalocean.com/community/tutorials/how-to-use-gpg-to-encrypt-and-sign-messages)
    1. Send Anish an encrypted email ([public key](https://keybase.io/anish)).
    1. Sign a Git commit with `git commit -S` or create a signed Git tag with
       `git tag -s`. Verify the signature on the commit with `git show
       --show-signature` or on the tag with `git tag -v`. -->
1. **熵**
    1. 假定一個密碼是由 5 個小寫的常規詞拼接組成，每個詞都是從含有 100,000 詞的字典內隨機選取，且每個詞選中概率相同，
       例如 `correcthorsebatterystaple`。這個密碼的熵是多少位元？
    1. 假定一個密碼由 8 個隨機字母數字組成（包括大小寫），例如 `rg8Ql34g`，它的熵是多少位元？
    1. 以上兩個密碼誰更強？
    1. 假定一個入侵者可以每秒嘗試 10,000 個密碼，平均來說，他需要多久來攻破上面兩個密碼？
1. **密碼雜湊函式** 從
   [Debian 鏡像站](https://www.debian.org/CD/http-ftp/)下載一個 Debian 映象
   （比如[這個阿根廷鏡像站](http://debian.xfree.com.ar/debian-cd/current/amd64/iso-cd/)）。
   檢查 hash 值（例如使用 `sha256sum` 指令），與域名爲 `debian.org` 的 Debian 官方站點提供的值（例如[這個
   檔案](https://cdimage.debian.org/debian-cd/current/amd64/iso-cd/SHA256SUMS)）進行對比。
1. **對稱加密** 透過 [OpenSSL](https://www.openssl.org/) 使用 AES 加密一個檔案：
   `openssl aes-256-cbc -salt -in {input filename} -out {output filename}`。
   使用 `cat` 或 `hexdump` 查看其內容。再使用 `openssl aes-256-cbc -d -in {input filename} 
   -out {output filename}` 來解密，並且使用 `cmp` 驗證解密內容與元檔案相同。
1. **非對稱加密**
    1. 在你擁有的電腦上（不要使用 Athena，因爲 Kerberos 與 SSH 密鑰的交互十分詭異）設定一個
     [SSH 密鑰](https://www.digitalocean.com/community/tutorials/how-to-set-up-ssh-keys--2)
     使用更安全的 [ED25519 密鑰](https://wiki.archlinux.org/index.php/SSH_keys#Ed25519)來替代 RSA 密鑰。
     確保你使用了密碼來加密私鑰。
    1. [設定 GPG](https://www.digitalocean.com/community/tutorials/how-to-use-gpg-to-encrypt-and-sign-messages)。
    1. 給 Anish 發送一封加密電郵 ([ Anish 的公鑰](https://keybase.io/anish))。
    1. 使用 `git commit -S` 簽署一個提交，或使用 `git tag -s` 建立一個帶有簽章的 Git tag。
    使用`git show --show-signature` 來檢查提交的簽章，或使用 `git tag -v` 檢查 tag。