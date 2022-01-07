> 原文：[Writing good commit messages](https://github.com/erlang/otp/wiki/Writing-good-commit-messages)

Good commit messages serve at least three important purposes:

* To speed up the reviewing process.
* To help us write a good release note.
* To help the future maintainers of Erlang/OTP (it could be you!), say five years into the future, to find out why a particular change was made to the code or why a specific feature was added.

良好的提交信息至少有三个重要的目的：

* 加快审查过程。
* 帮助我们写一个很好的发行版本说明。
* 为了帮助未来的 Erlang / OTP维护者（可能是你！），比如说将来的五年，要弄清楚为什么要对代码做出特别的改变，或者为什么添加了一个特定的功能。



Structure your commit message like this:

From: [http://git-scm.com/book/ch5-2.html]

> ```
> Short (50 chars or less) summary of changes
>
> More detailed explanatory text, if necessary.  Wrap it to about 72
> characters or so.  In some contexts, the first line is treated as the
> subject of an email and the rest of the text as the body.  The blank
> line separating the summary from the body is critical (unless you omit
> the body entirely); tools like rebase can get confused if you run the
> two together.
>
> Further paragraphs come after blank lines.
>
>   - Bullet points are okay, too
>
>   - Typically a hyphen or asterisk is used for the bullet, preceded by a
>     single space, with blank lines in between, but conventions vary here
> ```



你提交的消息结构如下所示：

来自：[http://git-scm.com/book/ch5-2.html]

> ```
> 短（50个字符或更少）更改的摘要
>
> 更详细的解释性文本，如有必要。 包裹约72字符等。 
> 在某些情况下，第一行就像电子邮件的主题，文本的其余部分为正文。 
> 将摘要与正文分开的空白行、线是至关重要的（除非你完全省略正文）;
> 如果你同时运行两个的话，像 rebase 这样的工具会变得困惑。
>
> 空白行后写更多的段落。
>
>    - 无序列表也是可以的。
>
>    - 在空白行之间，通常使用连字符或星号作为序号，前面加上一个空格，但讨论在这里
> ```



DO
--

* Write the summary line and description of what you have done in the imperative mode, that is as if you were commanding someone. Start the line with "Fix", "Add", "Change" instead of "Fixed", "Added", "Changed".
* Always leave the second line blank.
* Line break the commit message (to make the commit message readable without having to scroll horizontally in `gitk`).



* 在命令模式中写出你所做的总结和描述行，就好像你在命令某人一样。 以“Fix”，“Add”，“Change”而不是“Fixed”，“Added”，“Changed”开头。
* 始终将第二行留空。
* 用行来分离提交消息（使提交消息可读，而不必在`gitk`中水平滚动）。



DON'T
-----

* Don't end the summary line with a period - it's a title and titles don't end with a period.



* 不要用日期来结束一段摘要行 - 这是一个标题，而标题是不会以日期来结尾的。



Tips
----

* If it seems difficult to summarize what your commit does, it may be because it includes several logical changes or bug fixes, and are better split up into several commits using `git add -p`.



* 如果看起来难以总结你提交所做的工作，可能是因为它包含了几个逻辑更改或错误修复。可以使用`git add -p`更好地分成几个不同的提交。



References
----------

The following blog post has a nice discussion of commit messages:

"On commit messages":http://who-t.blogspot.com/2009/12/on-commit-messages.html
