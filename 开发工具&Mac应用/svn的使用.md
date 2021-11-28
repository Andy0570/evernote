# 引言
为什么使用SVN？在企业工作时，经常需要团队配合，这时一份工程需要多个人共
同维护，业内称之为**多人联编**。联编的方式有很多种，最原始的方式就是A写完代
码a,B写完代码b....M写完代码m，然后由N负责把a到m的所有代码进行整合，而
且N需要和所有人沟通，对所有代码都很清晰，整合时不能够出现差错，否则代码
就有可能达不到预期效果。很显然这个工作是非常繁重而且效率非常低的。作为地
球上最智慧的一群人，我们怎么能容忍这种低效率的工作呢。所以诞生了很多联编
工具，帮助我们自己解决各种问题。其中应用最广泛的莫过于SVN了，当然目前git
以其先进敏捷的特点，得到越来越多人的认可和使用，著名程序猿交友网站
[Github](https://github.com/)就是提供git服务的网站。[git和SVN的区别](http://blog.csdn.net/a117653909/article/details/8952183)

# 定义
[百度百科](http://baike.baidu.com/link?url=qbPNjchDLlpAyQ20mUABoCFeExjB3l1as-iS9bCx5Zk_CdsFnyPNjRGrbCnudAz84OL9xq7Y8gj8S7Cyr0InD_)给出SVN的定义：
> SVN是Subversion的简称，是一个开放源代码的版本控制系统，相较于RCS、CVS，它采用了分支管理系统，它的设计目标就是取代CVS。互联网上很多版本控制服务已从CVS迁移到Subversion。说得简单一点SVN就是用于多个人共同开发同一个项目，共用资源的目的。

# 搭建SVN服务器
服务器的作用就如同引言中所说的工程师N的作用，负责整合代码，并且还能记录版本信息。
服务器的搭建过程非常简单，我们以Windows为例，使用基于Apache构建的VisualSVN，可以
快速简单地搭建出SVN服务器。
* windows 环境请参考：[使用VisualSVN Server搭建服务器](http://www.jb51.net/article/17365.htm)
任何一台连接在局域网中的windows电脑都可以变成SVN服务器，如果把SVN托管到公网中，
那么在世界任何角落你都可以开心的提交你的代码了，常见于跨国、跨地区合作。
* Mac 环境请参考：[Mac环境下svn的使用](http://www.cnblogs.com/mjios/archive/2013/03/10/2952258.html)

# 使用SVN
在Mac OS X中，使用SVN可以使用命令行工具，也可以使用各种软件。我们先从命令行讲起，介绍几个简单的SVN命令。

## SVN基本命令：
### 1. 启动svn服务器
```
svnserve -d -r /Users/apple/svn
或者输入：svnserve -d -r /Users/apple/svn/mycode
```

### 2. 结束服务
```
killall -9 svnserve
```

### 3. CheckOut：将文件checkout到本地目录
```
svn checkout path（path是服务器上的目录）
例如：svn checkout svn://192.168.1.1/pro/domain
简写：svn co
```
### 4. Add：往版本库中添加新的文件
```
svn add file
例如：svn add test.php(添加test.php)
svn add *.php(添加当前..目录下所有的php.文件)
```
### 5. Commit：将改动的文件提交到版本库
```
svn commit -m “LogMessage“ [-N] [--no-unlock] PATH(如果选择了保持锁，就
使⽤用–no-unlock开关)
例如：svn commit -m “add test file for my test“ test.php
简写：svn ci
```
```
日常提交更改过的代码到服务器：
1. 打开终端，先定位到/Users/apple/Documents/code目录，输入：
cd /Users/apple/Documents/code
2. 输入提交指令：svn commit -m "修改日志说明"
```

### 6. Lock:unLock：加锁/解锁
```
svn lock -m "LockMessage" [--force] PATH
例如：svn lock -m "lock test file" test.php
svn unlock PATH
```
### 7. Update:更新到某个版本
```
svn update -r m path
例如：
svn update 如果后面没有目录，默认将当前目录以及子目录下的所有文件都更新到最新版本。
svn update -r 200 test.php(将版本库中的.文件test.php还原到版本200)
svn update test.php(更新，于版本库同步。如果在提交的时候提示过期的话，是因为冲突，需要先update，修改文件，然后清除svn resolved，最后再提交commit)
简写：svn up
```
```
日常更新服务器端的代码到客户端
1.  打开终端，先定位到/Users/apple/Documents/code目录，输入：
cd /Users/apple/Documents/code
2.  输入指令：svn update
```

### 8. Status:查看文件或者目录状态
```
1. svn status path（目录下的文件和子目录的状态，正常状态不显示）
【?：不在svn的控制中；M：内容被修改；C：发生冲突；A：预定加入到版本库；K：被锁定】
2. svn status -v path(显示文件和子目录状态)
第一列保持相同，第二列显示工作版本号，第三和第四列显示最后一次修改的版本号和修改人。
注：svn status、svn diff和 svn revert这三条命令在没有网络的情况下也可以执行的，原因是svn在本地的.svn中保留了本地版本的原始拷贝。
简写：svn st
```

### 9. Delete:删除文件
```
svn delete path -m "delete test file"
例如：
svn delete svn://192.168.1.1/pro/domain/test.php -m "delete test file"
或者直接svn delete test.php 然后再svn ci -m "delete test file" 推荐使用这种
简写：svn (del, remove, rm)
```
### 10. Log: 查看日志
```
svn log path
例如：svn log test.php 显示这个文件的所有修改记录，及其版本号的变化
```
>  提示：此处如果报错Item is not readable，则需要在
svnserve.conf 文件中,设置 anon-access = none。

### 11. Info:查看文件详细信息
```
svn info path
例如：svn info test.php
```
### 12. Diff:比较差异
```
svn diff path(将修改的文件与基础版本比较)
例如：svn diff test.php
svn diff -r m:n path (对版本m和版本n比较差异)
例如：svn diff -r 200:201 test.php
简写：svn di
```

# SVN图形界面工具软件
SVN图形界面工具软件有很多，在Mac环境下可以使用Versions、Cornerstone等等，这里我们只介绍一下Cornerstone的使用。

1.  Checkout
2.  Add
3.  Delete
4.  Revert
5.  Commit
6.  Update Update的重点在于如何解决冲突

# 遇到的问题
* 在 **Xcode 8.1** 上使用 **source control** —> **commit**时报错：
The working copy “XXX” failed to commit files.Couldn’t communicate with a helper application.
**解决方法：**：这里的提示可能只是svn帮助工具的一般处理，真正原因提示的并不是很明确，可以打开**终端**（command-line tool）使用命令行查看具体原因，然后再具体解决。代码如下：
```
$ cd [path]     //cd到项目根目录里
$ svn commit -m "这里写提交日志说明"
```
如果没问题的话会显示上传信息，有问题的话会输出错误原因，我这里提示的是：
>svn: E155010: Commit failed (details follow):
svn: E155010: '/Users/apple/Desktop//APP/Demo/Demo/Assets.xcassets/Others/appicon.imageset/appicon.png' is scheduled for addition, but is missing

  大概意思是之前在**Assets.xcassets**里计划增加的图片消失产生了错误，我找到了名称为：appicon 的Image Set，键盘**Del**删除之后再重新添加，再去**commit**就可以提交了。

  **总结：**：日常最好是通过Xcode添加和删除文件，以便正确更新svn状态（使用Finder操作文件可能会导致svn没法更新）。 如果发生提交错误，请转到命令行并使用svn status获取提示，出现错误，并从命令行进行修复。

### # [On OSX using sourcetree / git-svn getting “Can't locate SVN/Core.pm in @INC ”](https://stackoverflow.com/questions/16578465/on-osx-using-sourcetree-git-svn-getting-cant-locate-svn-core-pm-in-inc)

解决方法，实测有效：
```
sudo ln -s /Library/Developer/CommandLineTools/Library/Perl/5.18/darwin-thread-multi-2level/SVN /Applications/SourceTree.app/Contents/Resources/git_local/lib/perl5/site_perl/5.18.2/darwin-thread-multi-2level/SVN
sudo ln -s /Library/Developer/CommandLineTools/Library/Perl/5.18/darwin-thread-multi-2level/auto/SVN /Applications/SourceTree.app/Contents/Resources/git_local/lib/perl5/site_perl/5.18.2/darwin-thread-multi-2level/auto/SVN
```


### 参考文献
* [Mac环境下svn的使用](http://www.cnblogs.com/mjios/archive/2013/03/10/2952258.html)（阅读难度：★）
* [~~在Mac OS X下架设和使用Xcode的SVN版本管理环境~~](http://www.cocoachina.com/ios/20101201/2419.html) （阅读难度：★）
* [iOS 版本控制_ SVN / Mac 环境下 svn 的使用](http://www.jianshu.com/p/66f085556fb2) （阅读难度：★★）
* [GIT和SVN之间的五个基本区别](http://www.oschina.net/news/12542/git-and-svn)（阅读难度：★★★★）
* [iOS开发中SVN管理工具的使用](http://blog.devzeng.com/blog/ios-development-by-using-svn.html)
[图片上传失败...(image-28238b-1517827465013)]
