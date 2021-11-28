原文：[Deploying Slate](https://github.com/slatedocs/slate/wiki/Deploying-Slate)



Slate 会生成一系列静态的 HTML，JavaScript 和 CSS 文件，因此托管起来非常简单。



### 将你的文档发布到 GitHub Pages

发布你的 API 文档再简单不过了。

1. 确保你使用的是自己的帐户，而不是我们原来的仓库：`git remote show origin`。
2. 提交你对 markdown 源文件的修改：`git commit -a -m "Update index.md"`。
3. 将已更新的 markdown 源代码推送（push）到GitHub 仓库：`git push`。
4. 运行 `./deploy.sh`。

注意：使用 git 方式时，你不应在 github.com 上对你的仓库进行变更。

完成！你的更改现在应该已经在 <http://yourusername.github.io/slate> 上生效了。并且 master 分支也应该使用你已经编辑过的 markdown 文档更新了。请注意，如果这是你首次发布 Slate 文档，有时候可能需要十分钟左右才能在线上提供你的内容。即使不是第一次发布，也可能需要一些时间。

另外，还要感谢 [X1011](https://github.com/X1011/git-directory-deploy) 提供的出色的部署脚本。



### 将你的文档发布到你自己的服务器上

重申一遍，不要在生产服务器上运行 Middleman。Middleman 并不是被设计于可以安全地在公共服务器上运行的。它也并非旨在加速页面的加载。它纯粹用于开发。

你可以使用以下方法将静态文档发布到你自己的服务器上：

```bash
bundle exec middleman build --clean
```

Middleman 会将你的网站构建到项目的 `build` 目录下，并且你可以将这些静态 HTML 文件复制到你所选择的服务器。由于你只是复制静态文件，因此不需要在服务器上运行 Middleman。

另一种选择是使用 [middleman-deploy](https://github.com/middleman-contrib/middleman-deploy) gem。



### 在 GitHub 上设置自定义域名

如果你正在通过 GitHub Pages 托管 Slate 文档，那么设置自定义域名很简单！只需按照 [GitHub 帮助中心](https://help.github.com/articles/setting-up-a-custom-domain-with-github-pages/)的说明进行操作即可。请注意，不要将 `CNAME` 文件放在 Slate 项目的根目录中，而应该将其放在 `source` 文件夹中。当Middleman 发布到 `gh-pages` 分支时，它会将其复制到该分支的根文件夹下。

不幸的是，部署系统将覆盖你在 GitHub 中设置的任何自定义域名。这不是我们可以轻松解决的问题，这是GitHub 处理自定义域的方式的怪癖。因此，在他们修复该问题之前，请改用 `CNAME` 文件！


