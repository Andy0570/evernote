### 使用GitHub Desktop客户端上传Xcode代码

工程已经创建好了，但是创建的时候没有选择 **create Git**,现在想把做好的Demo上传到GitHub;

### 步骤

### 1.在本地创建Git：

打开终端，使用命令行创建一个新的存储库

```
cd /Users/Desktop/AppDemo
git init   // 初始化一个空的git仓库
git add .  // 此命令先将文件添加至暂存区域，但还没有提交
git commit -m "first commit"  // 提交
```

### 2.使用客户端上传

1. 在 mac 上下载 GitHub Desktop 客户端（[下载地址](https://desktop.github.com)），安装好后运行，输入 GitHub 的账户密码登陆;

2. 点击 GitHub 客户端菜单栏里的 **File** ➡ **Add Local Repository…**，选择自己的项目地址（该项目应该是已经有了 Git 本地仓库的）。 添加后，就会在**客户端上创建该项目的仓库**，但还没有在 GitHub 网站上创建；

3. 选中刚刚添加的仓库，点击 GitHub 客户端右上角的 **Publish** 按钮，就会**在 GitHub 的网站上创建了该项目的 git 空仓库**，因为本地项目的代码并没有上传到这个仓库；

   打开 GitHub 网站，可以发现该仓库是空的

   ![](https://ww1.sinaimg.cn/large/006tKfTcgy1fdnlkrhdi4j30r90gmdif.jpg)

4. 继续点击屏幕顶端 GitHub 菜单栏里的 Repository -> Push ，就会将本地代码提交到 GitHub 服务器上；

   > 这里我做到 第4步 的时候push不了。使用了命令行才上传成功的。

     ```
     git remote add origin https://github.com/githubName/AppDemo.git
     git push -u origin master
     ```

5. 完成了这一步之后，我们回到 Xcode 中就可以直接在 Xcode 里操作了，在 Xcode 里 commit，push，pull 等等操作时就会把代码直接交到服务器上




### 参考

* [xcode上传代码至GitHub](http://www.cnblogs.com/mzds/p/3723817.html)
* [Xcode中项目上传到GitHub](http://www.jianshu.com/p/a3c900a49b64)
