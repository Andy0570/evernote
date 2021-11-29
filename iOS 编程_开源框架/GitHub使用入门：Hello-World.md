![Github吉祥物 Octocat](http://upload-images.jianshu.io/upload_images/2648731-3ef41ffe024172f7.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 简介
**GitHub**是一个通过[Git](https://zh.wikipedia.org/wiki/Git)进行[版本控制](https://zh.wikipedia.org/wiki/%E7%89%88%E6%9C%AC%E6%8E%A7%E5%88%B6)的软件源代码托管服务，由GitHub公司（曾称Logical Awesome）的开发者Chris Wanstrath、PJ Hyett和Tom Preston-Werner使用[Ruby on Rails](https://zh.wikipedia.org/wiki/Ruby_on_Rails)编写而成。
GitHub同时提供**付费账户**和**免费账户**。这两种账户都可以创建公开的代码仓库，但是付费账户还可以创建私有的代码仓库。根据在2009年的Git用户调查，GitHub是最流行的Git访问站点.
除了允许个人和组织创建和访问保管中的代码以外，它也提供了一些方便社会化共同软件开发的功能，即一般人口中的社区功能，包括允许用户追踪其他用户、组织、软件库的动态，对软件代码的改动和bug提出评论等。GitHub也提供了图表功能，用于概观显示开发者们怎样在代码库上工作以及软件的开发活跃程度。
截止到2015年，GitHub已经有超过九百万注册用户和2110万代码库。
事实上已经成为了世界上最大的代码存放网站和开源社区
[更多:维基百科](https://zh.wikipedia.org/wiki/GitHub)

***
### 开始使用GitHub

本文是我对于[官方文档：Hello World](https://guides.github.com/activities/hello-world/) 的翻译。
![](http://upload-images.jianshu.io/upload_images/2648731-fb8ceb673c71c1b3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
**Hello World** 项目是计算机编程中一个历史悠久的传统。 这是一个在你开始学习新东西时候的简单练习。 让我们开始使用GitHub吧！
**您将学会：**
    
*  创建和使用存储库（repository）；    
*  启动和管理新分支（branch）；     
* 对文件进行更改并将其推送提交（commit）到GitHub ；    
* 打开并合并一个[Pull请求](https://www.zhihu.com/question/21682976)。  

#### 什么是GitHub？
GitHub是用于版本控制和协作的代码托管平台。 它可以让你和其他人在任何地方协同工作同一个项目。
本教程教你GitHub的要素，如*仓库(*repositories*)*，*分支(*branches*)*，*提交(*commits*)* 和*Pull请求*。 您将创建自己的**Hello World 项目**存储库，并学习GitHub的Pull Request工作流程，这是一种流行的创建和审查代码的方法。
##### 无需编码
要完成本教程，您需要一个[GitHub帐户](https://github.com/)和可以连接的Internet网络。 您不需要知道如何编码，使用命令行，或安装Git（GitHub的版本控制软件被建立于此）。
> 提示：在单独的浏览器窗口（或选项卡）中打开此指南，以便在完成教程中的步骤时可以看到它。

***
#### 步骤1.创建存储库(Repository)
**存储库(repository)**通常用于组织单个项目。 存储库可以包含文件夹和文件，图像，视频，电子表格和数据集 - 即项目需要的任何内容。 我们建议您包括一个README文件，或包含有关您的项目信息的文件。 GitHub使得在创建新存储库的同时添加一个README文件很容易实现。 它还提供其他常见选项，如许可证文件。
你的 ``hello-world`` 存储库 可以是一个你存储想法，资源，甚至与他人分享和讨论内容的地方。

##### 创建新存储库(Repository)
1. 在右上角，在您的头像或识别码旁边，单击 **+** 然后选择“Create new...”。
2. 命名您的存储库名称为``hello-world``。
3. 写一个简短的描述(description)。
4. 勾选 **Initialize this repository with a README**（使用README初始化此存储库。）
[图片上传失败...(image-7fa7f1-1513732778552)]
点击 **Create repository**（创建存储库）。

***
#### 步骤2.创建分支（Branch）
**分支（Branching）** 是同时在同一个存储库中创建不同版本的方式。
 默认情况下，你的仓库有一个名称为``master``的分支，它被认为是最终分支。 我们使用分支进行实验和编辑，然后将分支提交到``master``。
当您在主``master``分支上创建分支时，您就创建了主``master``节点在该时间点的副本或快照。 如果有人在您在分支上工作时对主``master``分支进行了更改，你就可以提取这些更新。
此图显示：    
 *  主分支 The ``master`` branch    
 *  一个新的分支称之为 映像（``feature``）（因为我们在这个分支上做“映像工作”）     
 * 在映像（``feature``）被合并到主 ``master``分支上的旅程  
[图片上传失败...(image-b704e5-1513732778552)]

 你曾经保存过一个文件的不同版本吗？ 就像是：
   *  story.txt
   * story-joe-edit.txt
   * story-joe-edit-reviewed.txt

 **分支(Branch)** 在GitHub存储库中实现类似的目标。
在GitHub上，我们的开发人员，作家和设计师使用分支来保持错误的修复和功能工作在独立于我们的主（产品）分支上。 当更改准备就绪时，它们将其分支合并到主master（）节点上。
##### 创建一个新的分支
1. 进入您的新存储库``hello-world``。    
2. 单击文件列表顶部叫做 **branch：master** 的下拉列表。     
3. 在新分支文本框中键入分支名称**readme-edits**。     
4. 选择蓝色的 **Create branch** 框或按键盘上的“Enter”。
[图片上传失败...(image-900a77-1513732778552)]
现在你有两个分支，``master``和``readme-edits``。 他们看起来完全一样，但不长久！ 接下来，我们将把更改添加到新分支。

***
 #### 步骤3.制作并提交更改
哇哦！ 现在，你在``readme-edits``分支的代码视图上，这是``master``的副本。 让我们做一些编辑。
在GitHub上，保存的更改称为**提交(commit)**。 每个提交都有一个相关的提交消息，这是一个描述为什么进行特定更改的描述。 提交消息会捕获您更改的历史记录，因此其他参与者可以了解您的操作以及原因。
##### 制作并提交更改
1. 单击``README.md``文件。
2. 在文件视图的右上角单击✎铅笔图标编辑。
3. 在编辑器中，写一点关于你自己的东西。
4. 编写提交消息来描述您的更改的内容。
5. 单击 **Commit changes** 提交更改按钮。
[图片上传失败...(image-8c610f-1513732778552)]
这些更改将只发生在``readme-edits``分支上的README文件，因此现在此分支包含的内容与``master``不同。

***
#### 步骤4.开启拉取请求（Pull Request）
干得漂亮！ 现在，您在``master``的分支中有更改，您可以开启拉取请求了。
Pull Request 请求是GitHub协作的核心。 当您开启拉取请求时，您就会提出您的更改和请求，并要求某人审核并提交您的贡献，并将其合并到他们的分支中。 拉取请求会显示来自两个分支的内容的差异或差异。 更改，添加和减少以绿色和红色显示。
一旦提交（commit）以后，您就可以开启一个拉取请求（Pull Request）并，在代码完成之前展开讨论。
通过在您的请求消息中使用GitHub的@[提醒系统](https://help.github.com/articles/about-writing-and-formatting-on-github/#text-formatting-toolbar)，您可以请求特定人员或团队的反馈，无论他们是在大厅还是在10个时区以外。
您甚至可以在您自己的存储库中开启拉取请求并自行合并它们。 这是一个在更大的项目之前学习GitHub流的伟大的方式。
#####　开启一个对README的更改的Pull Requset
点击图片获得更大的版本

| 步骤 | 截图 | 
| ------------- |:-------------:| 
| 单击 **Pull Requset** 选项卡，然后在拉请求页面，单击绿色的 **New pull request** 按钮。 | [图片上传失败...(image-eae31e-1513732778552)] | 
| 选择您所做的分支``readme-edits``，以与``master``（原始）进行比较。 | [图片上传失败...(image-92ee70-1513732778552)] | 
| 查看比较页面上的差异更改，确保它们是您要提交的内容。 | [图片上传失败...(image-72991b-1513732778552)] |
|当您确定这些是您要提交的更改时，单击大的绿色“**Create Pull Request**”按钮。 |[图片上传失败...(image-45bab3-1513732778552)] |
|给你的Pull Request写一个标题，并写一个简短的描述你的更改。 |[图片上传失败...(image-870051-1513732778552)] |

当你完成填写以上信息后，点击 **Create pull request**。
> 提示：您可以使用[表情符号](https://help.github.com/articles/basic-writing-and-formatting-syntax/#using-emoji)并将[图片和GIF](https://help.github.com/articles/file-attachments-on-issues-and-pull-requests/)拖放到你的评论和拉取请求上。

***
#### 步骤5.合并(merge)您的请求
在这最后一步，是时候把你的更改放在一起了 - 合并你的``readme-edits``分支到``master``分支。
1. 单击绿色**Merge pull request**按钮将更改合并到``master``中。
[图片上传失败...(image-fc048d-1513732778552)]     
2. 单击确认合并（**Confirm merge**）。     
3. 继续并删除分支，因为它的更改已被合并，删除分支（**Delete branch**）按钮在紫色框中。
[图片上传失败...(image-9a4969-1513732778552)]

*** 
#### 恭喜
通过完成本教程，您已经学会了创建一个项目，并在GitHub上发出pull request了
![:tada:](http://upload-images.jianshu.io/upload_images/2648731-dc773739fa00aba8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
![:octocat:](http://upload-images.jianshu.io/upload_images/2648731-20daba5a3d1d49bb.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
![:zap:](http://upload-images.jianshu.io/upload_images/2648731-85446d6ef1448565.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)！

这里是你在本教程中完成的：     
1. 创建了一个开源库    
2. 开始和管理一个新的分支    
3. 更改了一个文件并将这些更改提交给GitHub     
4. 开启并合并一个Pull Request

看看你的GitHub个人资料，你会看到你的新[贡献方块](https://help.github.com/articles/viewing-contributions-on-your-profile/)！
如果您想了解有关Pull Request的更多功能，我们建议您阅读[GitHub Flow Guide](https://guides.github.com/introduction/flow/)。 您还可以访问[GitHub探索](https://github.com/explore)并参与开源项目.
> 提示：请参阅我们的其他指南和YouTube频道，了解更多GitHub操作方法。

***

#### 参考
* [Git Cheat Sheet 中文版 ](https://github.com/flyhigher139/Git-Cheat-Sheet)
* [Git 的使用感受 @唐巧](http://blog.devtang.com/2012/02/03/talk-about-svn-and-git/)
* [Git 常用命令](http://www.devzhang.com/14540765848355.html)
* [怎样在Github上面贡献代码 @lark](https://github.com/larkjs/lark/wiki/怎样贡献代码)
* [Git及GitHub快速入门图文全面详解（30分钟学会）](http://www.jianshu.com/p/3f12bd3ccf2a)
* [GitFlow](https://datasift.github.io/gitflow/IntroducingGitFlow.html) ⭐️⭐️⭐️

#### 遇到的问题

* [GitHub Push出现的The local repository is out of date解决](http://blog.csdn.net/runintolove/article/details/50705807)

> 最后更新：20190105
