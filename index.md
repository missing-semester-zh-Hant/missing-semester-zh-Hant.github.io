---
layout: page
title: The Missing Semester of Your CS Education
---

<!-- Classes teach you all about advanced topics within CS, from operating systems
to machine learning, but there’s one critical subject that’s rarely covered,
and is instead left to students to figure out on their own: proficiency with
their tools. We’ll teach you how to master the command-line, use a powerful
text editor, use fancy features of version control systems, and much more! -->
計算機專業的課程會教授你從操作系統到機器學習等許多高級課題。
但有一重要主題卻甚少被講述，而時常被留給學生自己去學習：熟練使用工具。
我們將會教授你如何精於命令行、使用強大的文本編輯器，使用版本控制系統的精妙功能，與許多其他技巧！

<!-- Students spend hundreds of hours using these tools over the course of their
education (and thousands over their career), so it makes sense to make the
experience as fluid and frictionless as possible. Mastering these tools not
only enables you to spend less time on figuring out how to bend your tools to
your will, but it also lets you solve problems that would previously seem
impossibly complex. -->
在學生完成教育課程時，這些工具會陪伴他們數百小時（且工作中陪伴更久的時間）。
因此，流暢且無障礙地使用他們是很重要的。
熟練使用工具不僅可以減少浪費在調試工具上的時間，也使我們更容易解決困難問題。


<!-- Read about the [motivation behind this class](/about/). -->
閱讀[開設此課程的動機](/about/)

{% comment %}
# Registration

Sign up for the IAP 2020 class by filling out this [registration form](https://forms.gle/TD1KnwCSV52qexVt9).
{% endcomment %}

<!-- # Schedule -->
# 課程日程表

{% comment %}
**Lecture**: 35-225, 2pm--3pm<br>
**Office hours**: 32-G9 lounge, 3pm--4pm (every day, right after lecture)
{% endcomment %}

<ul>
{% assign lectures = site['2020'] | sort: 'date' %}
{% for lecture in lectures %}
    {% if lecture.phony != true %}
        <li>
        <strong>{{ lecture.date | date: '%-m/%d' }}</strong>:
        {% if lecture.ready %}
            <a href="{{ lecture.url }}">{{ lecture.title }}</a>
        {% else %}
            {{ lecture.title }} {% if lecture.noclass %}[no class]{% endif %}
        {% endif %}
        </li>
    {% endif %}
{% endfor %}
</ul>

<!-- Video recordings of the lectures are available [on
YouTube](https://www.youtube.com/playlist?list=PLyzOVJj3bHQuloKGG59rS43e29ro7I57J). -->
課程回放可於 [
YouTube](https://www.youtube.com/playlist?list=PLyzOVJj3bHQuloKGG59rS43e29ro7I57J)觀看.

<!-- # About the class -->
# 關於此課程

<!-- **Staff**: This class is co-taught by [Anish](https://www.anishathalye.com/), [Jon](https://thesquareplanet.com/), and [Jose](http://josejg.com/).
**Questions**: Email us at [missing-semester@mit.edu](mailto:missing-semester@mit.edu). -->

**講師**: 此課程由 [Anish](https://www.anishathalye.com/), [Jon](https://thesquareplanet.com/), 與 [Jose](http://josejg.com/)共同講授.
**若有疑問**: 請寄信至 [missing-semester@mit.edu](mailto:missing-semester@mit.edu).

<!-- # Beyond MIT -->
# MIT之外

<!-- We've also shared this class beyond MIT in the hopes that others may
benefit from these resources. You can find posts and discussion on -->
我們希望人們從此課程中收益，並於MIT之外也分享了此課程。可以在這些地方進行探討。

 - [Hacker News](https://news.ycombinator.com/item?id=22226380)
 - [Lobsters](https://lobste.rs/s/ti1k98/missing_semester_your_cs_education_mit)
 - [/r/learnprogramming](https://www.reddit.com/r/learnprogramming/comments/eyagda/the_missing_semester_of_your_cs_education_mit/)
 - [/r/programming](https://www.reddit.com/r/programming/comments/eyagcd/the_missing_semester_of_your_cs_education_mit/)
 - [Twitter](https://twitter.com/jonhoo/status/1224383452591509507)
 - [YouTube](https://www.youtube.com/playlist?list=PLyzOVJj3bHQuloKGG59rS43e29ro7I57J)

<!-- # Translations -->
# 譯文

- [簡體中文: missing-semester-cn.github.io](https://missing-semester-cn.github.io/)

<!-- Note: these are external links to community translations. We have not vetted
them. -->
敬請注意: 這些是社區貢獻的譯文的外部鏈接，我們沒有驗證其內容。

<!-- Have you created a translation of the course notes from this class? Submit a
[pull request](https://github.com/missing-semester/missing-semester/pulls) so
we can add it to the list! -->
若您創作了此課程新的譯文，請提交
[pull request](https://github.com/missing-semester/missing-semester/pulls) 到此處，
以便可以將您位列於此！

<!-- ## Acknowledgements -->
## 謝辭

<!-- We thank Elaine Mello, Jim Cain, and [MIT Open
Learning](https://openlearning.mit.edu/) for making it possible for us to
record lecture videos; Anthony Zolnik and [MIT
AeroAstro](https://aeroastro.mit.edu/) for A/V equipment; and Brandi Adams and
[MIT EECS](https://www.eecs.mit.edu/) for supporting this class. -->

感謝 Elaine Mello, Jim Cain, 與 [MIT Open
Learning](https://openlearning.mit.edu/) 錄製的課程回放; Anthony Zolnik 與 [MIT
AeroAstro](https://aeroastro.mit.edu/) 提供的影像設施; 以及 Brandi Adams 與
[MIT EECS](https://www.eecs.mit.edu/) 對此課程的支援.

---

<div class="small center">
<p><a href="https://github.com/missing-semester/missing-semester">Source code</a>.</p>
<p>Licensed under CC BY-NC-SA.</p>
<p>See <a href="/license/">here</a> for contribution &amp; translation guidelines.</p>
</div>
