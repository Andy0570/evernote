![](http://upload-images.jianshu.io/upload_images/2648731-c9116e526f39a463.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

[App Store购买链接（¥30 价格其实还可以接受，请支持正版）](https://itunes.apple.com/cn/app/expressions/id913158085?mt=12) | [Expressions 官网](http://www.apptorium.com/expressions)


## 特性

* 极简化的UI界面；
* 语法高亮显示；
* 参考表；
* 实时预览；
* 正则表达式语法库提示；
* 组突出显示；
* 组之间轻松切换（快捷键：`cmd+shift+}` or `cmd+shift+{`）;
* 漂亮的全屏视图；
* 多行模式和注释；
* 支持亮暗两种主题；




## 正则表达式

正则表达式，又称规则表达式。（英语：Regular Expression，在代码中常简写为 regex、regexp 或 RE），计算机科学的一个概念。它是对字符串操作的一种逻辑公式。正则表达式可以检测给定的字符串是否符合我们定义的逻辑，也可以从字符串中获取我们想要的特定部分。它可以迅速地用极简单的方式达到字符串的复杂控制。
正则表通常被用来检索、替换那些符合某个模式（规则）的文本。
 [维基百科](https://zh.wikipedia.org/wiki/%E6%AD%A3%E5%88%99%E8%A1%A8%E8%BE%BE%E5%BC%8F)、[百度百科](http://baike.baidu.com/link?url=T0mre4ve9IB6k9BGHmMm9N1_xV6J-VXWKitvjZWxt4ek2e5h0AJCNBmcX1s5QloJx1BbQbRqppFNDKEmo339jOrYum9wgJGYUrs8FAs7-xLme2mLspbYQpsljkqBX5-2aTw4bjC-D9v78IkeBeqKi_)



## Basic - 基本语法

### Metacharacters - 元字符

表示字符集或特殊字符。

| 正则表达式 | 说明                                       | 描述                                       |
| ----- | ---------------------------------------- | ---------------------------------------- |
| .     | any character                            | 任意字符                                     |
| ^     | beginning of a line                      | 一行的开始字符                                  |
| $     | end of a line                            | 一行的末尾字符                                  |
| \     | quotes special characters (* ? + [ ] ( ) { } ^ $ \| \ . / ) | 特殊字符的转义符号（`* ? + [ ] ( ) { } ^ $ \| \ . / `）<br>与括号中的特殊字符配合使用，如 `\?`、`\\` |
| \w    | word                                     | 所有的文字数字式字符：大小写字母、数字和下划线。<br>同 `[a-zA-Z0-9_]` |
| \t    | horizontal tabulation                    | 水平制表符                                    |
| \n    | new line                                 | 换行符                                      |
| \d    | any digit                                | 任何数字。<br>同 `[0-9]`                              |
| \s    | white space character (\t \n \f \r \p{Z}) | 所有空白字符(`\t` 制表符 `\n` 换行 `\f` 换页符 `\r` 回车 `\p{Z}`) |
| [...] | match any character (or range of characters) inside the brackets. Range may be e.g. [a-z], [A-Z], [3…5], etc. | 括号内的字符集合、字符区间或数字范围。<br>例如：`[a-z]`、`[A-Z]`、`[3...5]`|
| \D    | any character that is not a decimal digit | 任何非数字。<br>同 `[^0-9]`                         |
| \S    | non-white space character                | 任何非空白字符                                    |
| \W    | non-word character                       | 任何非文字数字式字符。<br>同 `[^a-zA-Z0-9_]`               |



### Operators - 运算符

运算符允许描述一个表达式（或子表达式）应如何匹配。

| 正则表达式 | 说明                                       | 描述                     |
| ----- | ---------------------------------------- | ---------------------- |
| (...) | groups expression into subexpressions    | 将表达式分组成子表达式            |
| A\|B  | alternation. Matches A or B              | 逻辑运算符“或”。 匹配 A 或 B              |
| ^     | negation                                 | 否定表达式，如`[^0-9]`，表示匹配非数字 |
| *     | match 0 or more times                    | 匹配0次或者多次               |
| +     | match 1 or more times                    | 匹配1次或者多次               |
| ?     | match zero or one times                  | 匹配0次或者1次               |
| {n}   | match exactly n times                    | 精确匹配 n 次                 |
| {n,}  |                                          | 至少匹配 n 次                 |
| {n,m} | match at least n time and not more than m | 匹配次数区间，至少 n 次，至多 m 次       |



### Non-capturing operators -非捕获运算符

| 正则表达式     | 说明                                       | 描述                           |
| --------- | ---------------------------------------- | ---------------------------- |
| (?#...)   | Comment                                  | 注释                           |
| (? : ...) | Subexpression must occur but it's not captured in group. | 子表达式必须发生，但不会在组中捕获。           |
| (? ! ...) | Makes sure that the subexpression does not occur at current position. It's useful to exclude part of expression. | 确保在当前位置不会发生子表达式。 排除部分表达式很有用。 |



# Advanced - 高级用法

### Metacharacters - 元数据

| 正则表达式       | 说明                                       | 描述                                       |
| ----------- | ---------------------------------------- | ---------------------------------------- |
| \a          | Match a BELL, \u0007                     | 匹配响铃符号，`\u0007`                            |
| \A          | Match at the beginning of the input.Differs from ^ in that \A will not match after a new line within the input. | 输入开始时匹配，与`^`符号不同，`\A`不会匹配输入后的新行              |
| \b          | Match if the current position is a word boundary.Boundaries occur at the transitions between word (\w) and non-word (\W) characters, with combining marks ignored. | 如果当前位置是字边界则匹配。边界出现在文字数字式字符（`\w`）和非文字数字式字符（`\W`）字符之间的转换处，组合标记被忽略。 |
| \B          | Match if the current position is not a word boundary. | 如果当前位置不是文字数字式字符边界则匹配。                    |
| \cX         | Match a control-X character.             | 匹配一个control-X字符。                         |
| \d          | Match any character with the Unicode General Category of Nd (Number, Decimal Digit.) | 匹配任何字符与Unicode通用类别的Nd（数字，十进制数字）。         |
| \D          | Match any character that is not a decimal digit. | 匹配任何不是十进制数字的字符。                          |
| \e          | Match an ESCAPE, \u001B.                 | 匹配ESCAPE，`\u001B`。                        |
| \E          | Terminates a \Q…\Equoted sequence.       | 终止\ Q ... \ E引用的序列。                      |
| \f          | Match a FORM FEED, \u000C.               | 匹配FORM FEED，`\u000C`。                     |
| \G          | Match if the current position is at the end of the previous match. | 如果当前位置在上一个匹配结束时匹配。                       |
| \h          | Match a Horizontal White Space character. They are characters with Unicode General Category of Space_Separator plus the ASCII tab (\u0009). | 匹配水平白色空格字符。 它们是具有Unicode通用类别Space_Separator和ASCII选项卡（`\u0009`）的字符。 |
| \H          | Match a non-Horizontal White Space character. | 匹配非水平白色空格字符。                             |
| \k          | Named Capture Back Reference.            | 命名捕获返回参考。                                |
| \n          | Match a line feed, \u000A.               | 匹配换行符 `\u000A`。                            |
| \N{unicode} | Match the named character.               | 匹配命名的字符。                                 |
| \p{unicode} | Match any character with the specified Unicode Property. | 与指定的Unicode属性匹配任何字符。                     |
| \P{unicode} | Match any character not having the specified Unicode Property. | 匹配任何不具有指定Unicode属性的字符。                   |
| \Q          | Quotes all following characters until \E. | 引用所有以下字符直到\ E。                           |
| \r          | Match a carriage return, \u000D.         | 匹配回车，\ u000D。                            |
| \R          | Match a new line character, or the sequence CR LF. | 匹配新的行字符或序列CR LF。                         |
| \s          | Match a white space character. White space is defined as [\t\n\f\r\p{Z}]. | 匹配一个空白字符。 空格被定义为 `[\t \n \f \r \p{Z}]`。 |
| \S          | Match a non-white space character.       |                                          |
| \t          | Match a horizontal tabulation, \u0009.   | 匹配水平制表符，`\u0009`。                         |
| \uhhhh      | Match the character with the hex value hhhh. | 匹配十六进制字符hhhh。                            |
| \uhhhhhhh   | Match the character with the hex value hhhhhhhh.Exactly eight hex digits must be provided, even though the largest Unicode code point is \U0010ffff. | 匹配十六进制字符hhhhhhhh。必须提供 8 位十六进制数字，即使最大的Unicode代码点是 `\U0010ffff`。 |
| \v          | Match a new line character.              | 匹配一个新的行字符。                               |
| \V          | Match a non-new line character.          |                                          |
| \w          | Match a word character.                  | 匹配一个字符                                   |
| \W          | Match a non-word character.              |                                          |
| \x{hhhh}    | Match the character with hex value hhhh.From one to six hex digits may be supplied. | 匹配十六进制字符hhhh。可提供1到6个十六进制数字。              |
| \xhh        | Match the character with two digit hex value hh | 匹配两位十六进制字符hh                             |
| \X          | Match a Grapheme Cluster .               | 匹配一个大写字母集                                |
| \Z          | Match if the current position is at the end of input, but before the final line terminator, if one exists. | 如果当前位置在输入结尾，但在最后一行终止符之前，如果存在，则进行匹配。      |
| \z          | Match if the current position is at the end of input. | 如果当前位置在输入结束时匹配。                          |
| \n          | Back Reference. Match whatever the nth capturing group matched. n must be a number > 1 and < total number of capture groups in the pattern. | 返回参考。 匹配任何第n个捕获组匹配。 n必须是数字> 1和<模式中捕获组的总数。 |
| \0ooo       | Match an Octal character. 'ooo' is from one to three octal digits. 0377 is the largest allowed Octal character. The leading zero is required; it distinguishes Octal constants from back references. | 匹配八进制字符 “ooo”是从一到三个八进制数字。 0377是允许的最大八进制字符。 领先的零是必需的; 它将八进制常量与反向引用区分开来。 |
| [...]       | Match any one character from the set.    | 匹配集合中的任何一个字符。                            |
| .           | Match any character.                     |                                          |
| ^           | Match at the beginning of a line.        |                                          |
| $           | Match at the end of a line.              |                                          |
| \           | Quotes the following character. Characters that must be quoted to be treated as literals are * ? + [ ( ) { } ^ $ \| \ . | 引用以下字符。必须引用被视为文字的字符是`* ？+ [（）{} ^ $ \| \` |
| \           | Quotes the following character. Characters that must be quoted to be treated as literals are [ ] \ | 引用以下字符 ,必须引用被视为文字的字符是[] \                |



### Operators - 运算符

| \|               | Alternation. A\|B matches either A or B. | 或者                                       |
| ---------------- | ---------------------------------------- | ---------------------------------------- |
| *                | Match 0 or more times. Match as many times as possible. | 匹配 0 次以上。 匹配尽可能多的次数。                       |
| +                | Match 1 or more times. Match as many times as possible. | 匹配 1 次以上。 匹配尽可能多的次数。                       |
| ？                | Match zero or one times. Prefer one.     | 匹配0次或1次。 偏向于1次                           |
| {n}              | Match exactly n times                    | 匹配 n 次                                     |
| {n,}             | Match at least n times. Match as many times as possible. | 匹配至少 n 次                                   |
| {n,m}            | Match between n and m times. Match as many times as possible, but not more than m. | 匹配 n~m 次                                   |
| *?               | Match 0 or more times. Match as few times as possible. | 加后缀 `?` 表示非贪婪匹配（匹配尽可能少的字符）                  |
| +?               | Match 1 or more times.Match as few times as possible. |                                          |
| ??               | Match zero or one times. Prefer zero.    |                                          |
| {n}?             | Match exactly n times                    |                                          |
| {n,}?            | Match at least n time§s, but no more than required for an overall pattern match |                                          |
| {n,m}?           | Match between n and m times. Match as few times as possible, but not less than n. |                                          |
| *+               | Match 0 or more times. Match as many times as possible when first encountered, do not retry with fewer even if overall match fails (Possessive Match) | 匹配0次以上。 在第一次遇到时，尽可能多地匹配，即使整体匹配失败（Possessive Match）也不要重试， |
| ++               | Match 1 or more times. Possessive match. |                                          |
| ?+               | Match zero or one times.  Possessive match. |                                          |
| {n}+             | Match exactly n times                    |                                          |
| {n,}+            | Match at least n times.  Possessive Match. |                                          |
| {n,m}+           | Match between n and m times. Possessive Match. |                                          |
| (...)            | Capturing parentheses. Range of input that matched the parenthesized subexpression is available after the match. |                                          |
| (? : ...)        | Non-capturing parentheses. Groups the included pattern, but does not provide capturing of matching text. Somewhat more efficient than capturing parentheses. | 非捕获括号。 对包含的模式进行分组，但不提供匹配文本的捕获。 比捕捉括号更有效率。 |
| (? > ...)        | Atomic-match parentheses. First match of the parenthesized subexpression is the only one tried; if it does not lead to an overall pattern match, back up the search for a match to a position before the "(?>" |                                          |
| (?#...)          | Free-format comment (?# comment ).       |                                          |
| (?=...)          | Look-ahead assertion. True if the parenthesized pattern matches at the current input position, but does not advance the input position. |                                          |
| (?!...)          | Negative look-ahead assertion. True if the parenthesized pattern does not match at the current input position. Does not advance the input position. |                                          |
| (?<=...)         | Look-behind assertion. True if the parenthesized pattern matches text preceding the current input position, with the last character of the match being the input character just before the current position. Does not alter the input position. The length of possible strings matched by the look-behind pattern must not be unbounded (no * or + operators.) |                                          |
| (?...)           | Negative Look-behind assertion. True if the parenthesized pattern does not match text preceding the current input position, with the last character of the match being the input character just before the current position. Does not alter the input position. The length of possible strings matched by the look-behind pattern must not be unbounded (no * or + operators.) |                                          |
| (?...)           | Named capture group. The are literal - they appear in the pattern. |                                          |
| (?ismwx-ismwx:…) | Flag settings. Evaluate the parenthesized expression with the specified flags enabled or -disabled. |                                          |
| (?ismwx-ismwx)   | Flag settings. Change the flag settings. Changes apply to the portion of the pattern following the setting. For example, (?i) changes to a case insensitive match. |                                          |



### Set Expressions - 设置表达式

| 正则表达式               | 说明                                       | 描述   |
| ------------------- | ---------------------------------------- | ---- |
| [abc]               | Match any of the characters a, b or c    |      |
| [^abc]              | Negation - match any character except a, b or c |      |
| [A-M]               | Range - match any character from A to M.  The characters to include are determined by Unicode code point ordering. |      |
| [\u0000-\U0010ffff] | Range - match all characters.            |      |



### Flags 

| 正则表达式                                    | 说明                                       | 描述   |
| ---------------------------------------- | ---------------------------------------- | ---- |
| i                                        | If set, matching will take place in a case-insensitive manner. |      |
| x                                        | If set, allow use of white space and #comments within patterns |      |
| s                                        | If set, a "." in a pattern will match a line terminator in the input text. By default, it will not. Note that a carriage-return / line-feed pair in text behave as a single line terminator, and will match a single "." in a RE pattern. |      |
| m                                        | Control the behavior of `^`and `$` in a pattern. By default these will only match at the start and end, respectively, of the input text. If this flag is set, `^` and `$` |    多行匹配`(?m)`  |
| will also match at the start and end of each line within the input text. | Controls the behavior of \b in a pattern. If set, word boundaries are found according to the definitions of word found in Unicode UAX 29, Text Boundaries. By default, word boundaries are identified by means of a simple classification of characters as either “word” or “non-word”, which approximates traditional regular expression behavior. The results obtained with the two options can be quite different in runs of spaces and other non-word characters. |      |




#### 贪婪匹配和非贪婪匹配的区分

* **贪婪匹配**：匹配尽可能多的字符。也就是说，正则表达式总是寻找最大的匹配，而不是最小的，这是故意设计的。
* **非贪婪匹配**：匹配尽可能少的字符。

| 贪婪量词  | 非贪婪量词  |      |
| ----- | ------ | ---- |
| *     | *?     |      |
| +     | +?     |      |
| ?     | ??     |      |
| {n}   | {n}?   |      |
| {n,}  | {n,}?  |      |
| {n,m} | {n,m}? |      |


##### 示例

正则表达式：`a.*z`
默认是贪婪匹配，正则表达式引擎会尽可能地匹配满足条件的最大区间。

![](http://upload-images.jianshu.io/upload_images/2648731-857cb63c0b474b79.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

正则表达式：a.*?z
在贪婪量词后面加`?` 即可实现非贪婪匹配。

![](http://upload-images.jianshu.io/upload_images/2648731-fb9be1b4c19da9fa.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 常用正则表达式

#### 一、校验数字的表达式
```
 1 数字：^[0-9]*$
 2 n位的数字：^\d{n}$
 3 至少n位的数字：^\d{n,}$
 4 m-n位的数字：^\d{m,n}$
 5 零和非零开头的数字：^(0|[1-9][0-9]*)$
 6 非零开头的最多带两位小数的数字：^([1-9][0-9]*)+(.[0-9]{1,2})?$
 7 带1-2位小数的正数或负数：^(\-)?\d+(\.\d{1,2})?$
 8 正数、负数、和小数：^(\-|\+)?\d+(\.\d+)?$
 9 有两位小数的正实数：^[0-9]+(.[0-9]{2})?$
10 有1~3位小数的正实数：^[0-9]+(.[0-9]{1,3})?$
11 非零的正整数：^[1-9]\d*$ 或 ^([1-9][0-9]*){1,3}$ 或 ^\+?[1-9][0-9]*$
12 非零的负整数：^\-[1-9][]0-9"*$ 或 ^-[1-9]\d*$
13 非负整数：^\d+$ 或 ^[1-9]\d*|0$
14 非正整数：^-[1-9]\d*|0$ 或 ^((-\d+)|(0+))$
15 非负浮点数：^\d+(\.\d+)?$ 或 ^[1-9]\d*\.\d*|0\.\d*[1-9]\d*|0?\.0+|0$
16 非正浮点数：^((-\d+(\.\d+)?)|(0+(\.0+)?))$ 或 ^(-([1-9]\d*\.\d*|0\.\d*[1-9]\d*))|0?\.0+|0$
17 正浮点数：^[1-9]\d*\.\d*|0\.\d*[1-9]\d*$ 或 ^(([0-9]+\.[0-9]*[1-9][0-9]*)|([0-9]*[1-9][0-9]*\.[0-9]+)|([0-9]*[1-9][0-9]*))$
18 负浮点数：^-([1-9]\d*\.\d*|0\.\d*[1-9]\d*)$ 或 ^(-(([0-9]+\.[0-9]*[1-9][0-9]*)|([0-9]*[1-9][0-9]*\.[0-9]+)|([0-9]*[1-9][0-9]*)))$
19 浮点数：^(-?\d+)(\.\d+)?$ 或 ^-?([1-9]\d*\.\d*|0\.\d*[1-9]\d*|0?\.0+|0)$
```
#### 二、校验字符的表达式
```
 1 汉字：^[\u4e00-\u9fa5]{0,}$
 2 英文和数字：^[A-Za-z0-9]+$ 或 ^[A-Za-z0-9]{4,40}$
 3 长度为3-20的所有字符：^.{3,20}$
 4 由26个英文字母组成的字符串：^[A-Za-z]+$
 5 由26个大写英文字母组成的字符串：^[A-Z]+$
 6 由26个小写英文字母组成的字符串：^[a-z]+$
 7 由数字和26个英文字母组成的字符串：^[A-Za-z0-9]+$
 8 由数字、26个英文字母或者下划线组成的字符串：^\w+$ 或 ^\w{3,20}$
 9 中文、英文、数字包括下划线：^[\u4E00-\u9FA5A-Za-z0-9_]+$
10 中文、英文、数字但不包括下划线等符号：^[\u4E00-\u9FA5A-Za-z0-9]+$ 或 ^[\u4E00-\u9FA5A-Za-z0-9]{2,20}$
11 可以输入含有^%&',;=?$\"等字符：[^%&',;=?$\x22]+
12 禁止输入含有~的字符：[^~\x22]+
```

#### 三、特殊需求表达式
```
 1 Email地址：^\w+([-+.]\w+)*@\w+([-.]\w+)*\.\w+([-.]\w+)*$
 2 域名：[a-zA-Z0-9][-a-zA-Z0-9]{0,62}(/.[a-zA-Z0-9][-a-zA-Z0-9]{0,62})+/.?
 3 InternetURL：[a-zA-z]+://[^\s]* 或 ^http://([\w-]+\.)+[\w-]+(/[\w-./?%&=]*)?$
 4 手机号码：^(13[0-9]|14[0-9]|15[0-9]|166|17[0-9]|18[0-9]|19[8|9])\d{8}$
 5 电话号码("XXX-XXXXXXX"、"XXXX-XXXXXXXX"、"XXX-XXXXXXX"、"XXX-XXXXXXXX"、"XXXXXXX"和"XXXXXXXX)：^(\(\d{3,4}-)|\d{3.4}-)?\d{7,8}$ 
 6 国内电话号码(0511-4405222、021-87888822)：\d{3}-\d{8}|\d{4}-\d{7} 
 7 18位身份证号码(数字、字母x结尾)：^((\d{18})|([0-9x]{18})|([0-9X]{18}))$
 8 帐号是否合法(字母开头，允许5-16字节，允许字母数字下划线)：^[a-zA-Z][a-zA-Z0-9_]{4,15}$
 9 密码(以字母开头，长度在6~18之间，只能包含字母、数字和下划线)：^[a-zA-Z]\w{5,17}$
10 👍强密码(必须包含大小写字母和数字的组合，不能使用特殊字符，长度在8-10之间)：^(?=.*\d)(?=.*[a-z])(?=.*[A-Z]).{8,10}$  
11 日期格式：^\d{4}-\d{1,2}-\d{1,2}
12 一年的12个月(01～09和1～12)：^(0?[1-9]|1[0-2])$
13 一个月的31天(01～09和1～31)：^((0?[1-9])|((1|2)[0-9])|30|31)$ 
14 钱的输入格式：
15    1.有四种钱的表示形式我们可以接受:"10000.00" 和 "10,000.00", 和没有 "分" 的 "10000" 和 "10,000"：^[1-9][0-9]*$ 
16    2.这表示任意一个不以0开头的数字,但是,这也意味着一个字符"0"不通过,所以我们采用下面的形式：^(0|[1-9][0-9]*)$ 
17    3.一个0或者一个不以0开头的数字.我们还可以允许开头有一个负号：^(0|-?[1-9][0-9]*)$ 
18    4.这表示一个0或者一个可能为负的开头不为0的数字.让用户以0开头好了.把负号的也去掉,因为钱总不能是负的吧.下面我们要加的是说明可能的小数部分：^[0-9]+(.[0-9]+)?$ 
19    5.必须说明的是,小数点后面至少应该有1位数,所以"10."是不通过的,但是 "10" 和 "10.2" 是通过的：^[0-9]+(.[0-9]{2})?$ 
20    6.这样我们规定小数点后面必须有两位,如果你认为太苛刻了,可以这样：^[0-9]+(.[0-9]{1,2})?$ 
21    7.这样就允许用户只写一位小数.下面我们该考虑数字中的逗号了,我们可以这样：^[0-9]{1,3}(,[0-9]{3})*(.[0-9]{1,2})?$ 
22    8.1到3个数字,后面跟着任意个 逗号+3个数字,逗号成为可选,而不是必须：^([0-9]+|[0-9]{1,3}(,[0-9]{3})*)(.[0-9]{1,2})?$ 
23    备注：这就是最终结果了,别忘了"+"可以用"*"替代如果你觉得空字符串也可以接受的话(奇怪,为什么?)最后,别忘了在用函数时去掉去掉那个反斜杠,一般的错误都在这里
24 xml文件：^([a-zA-Z]+-?)+[a-zA-Z0-9]+\\.[x|X][m|M][l|L]$
25 中文字符的正则表达式：[\u4e00-\u9fa5]
26 双字节字符：[^\x00-\xff]    (包括汉字在内，可以用来计算字符串的长度(一个双字节字符长度计2，ASCII字符计1))
27 空白行的正则表达式：\n\s*\r    (可以用来删除空白行)
28 HTML标记的正则表达式：<(\S*?)[^>]*>.*?</\1>|<.*? />    (网上流传的版本太糟糕，上面这个也仅仅能部分，对于复杂的嵌套标记依旧无能为力)
29 首尾空白字符的正则表达式：^\s*|\s*$或(^\s*)|(\s*$)    (可以用来删除行首行尾的空白字符(包括空格、制表符、换页符等等)，非常有用的表达式)
30 腾讯QQ号：[1-9][0-9]{4,}    (腾讯QQ号从10000开始)
31 中国邮政编码：[1-9]\d{5}(?!\d)    (中国邮政编码为6位数字)
32 IP地址：\d+\.\d+\.\d+\.\d+    (提取IP地址时有用)
33 IP地址：((?:(?:25[0-5]|2[0-4]\\d|[01]?\\d?\\d)\\.){3}(?:25[0-5]|2[0-4]\\d|[01]?\\d?\\d))    (由@飞龙三少 提供,感谢共享)

👍 6到20位的字符： ^[a-zA-Z0-9~!@#%&*.,_?/\\\\]{6,20}$
👍 6到20位的字符，不能是纯数字：^(?=.*[a-zA-Z~!@#%&*.,_?/\\\\])(?=.*[a-zA-Z0-9~!@#%&*.,_?/\\\\]).{6,20}$
```


### 参考

* 完整的正则表达式文档：[ICU User Guide](http://userguide.icu-project.org/strings/regexp)
* [Regular Expression Language - Quick Reference](https://docs.microsoft.com/en-us/dotnet/standard/base-types/regular-expression-language-quick-reference?redirectedfrom=MSDN)
* [《系统地学习正则表达式(一)：基础篇》 @iOS_小松哥](http://www.jianshu.com/p/ac2596be9606)
* [《系统地学习正则表达式(二)：进阶篇》  @iOS_小松哥](http://www.jianshu.com/p/555f85023a7f)
* [最全的常用正则表达式大全——包括校验数字、字符、一些特殊的需求等等](http://www.cnblogs.com/zxin/archive/2013/01/26/2877765.html)
* [iOS 正则表达式语法全集](https://github.com/pro648/tips/wiki/iOS%E6%AD%A3%E5%88%99%E8%A1%A8%E8%BE%BE%E5%BC%8F%E8%AF%AD%E6%B3%95%E5%85%A8%E9%9B%86)
* [密码强度正则表达式 – 必须包含大写字母，小写字母和数字，至少 8 个字符等](https://www.html.cn/archives/8100)
