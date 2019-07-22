
# GitHub项目初始化建议

----

## 前言


现代做为一个程序员无论code如何都会在github上学习各种知识及发布自己的一点内容，那么如何让自己发布的项目更专业，甚至取的一点点小星星呢，让自己的项目变的更专业或者漂亮，也方便其他人了解。在此简单做个归纳，当然做到这些仅仅是表面功夫，代码的内在修炼还需自我提升，不要仅仅金玉其外哦。


## README

README 是用户点进你项目的第一印象，值得你去 [学习一下如何写readme](https://www.zhihu.com/question/299390628)。当然我们自己可能考虑的不是很完全，不如使用工具来生成。推荐 [readme-md-generator](https://github.com/kefranabg/readme-md-generator)。

### 合适的LICENSE

> 在二十世纪而七十年代末和八十年代初，为了防止自己的软件被竞争对手所使用，大多数厂家停止分发其软件源代码，并开始使用版权和限制性软件许可证，来限制或者禁止软件源代码的复制或再分配。随后，Richard Matthew Stallman（Richard Matthew Stallman）发起了自由软件运动，他开创了 Copyleft 的概念：使用版权法的原则来保护使用、修改和分发自由软件的权利，并且是描述这些术语的自由软件许可证的主要作者。最为人所称道的是GPL（被z广泛使用的自由软件协议）


- 简单来说,如当你把代码放置到公有领域，就意味着任何人可以修改，并且不需要标明出注；
- 可如果你想要别人标明出处及作者，你就需要 MIT 协议；
- 而你希望别人闭源的话，那么你就需要 MPL 协议等等。



### Logo

好的项目第一步就要找一个比较好看得 logo，现在 AI 自动生成 logo 非常火爆。推荐几个网站

- [brandmark](http://brandmark.io)
- [looka](https://looka.com)
- [launchaco](https://www.launchaco.com/logo/)，这个是完全免费的，虽然 logo 感觉不太好看
- [designevo](https://www.designevo.com/logo-maker/)，会给你生成一张基础的 logo 图（jpg，和 png），如果你想要更定制的，就要收费了

### Badge

在 README 里面，我们可以添加 [Badge(徽章)](https://shields.io)，常用的徽标主要有持续集成状态、项目版本信息、代码测试覆盖率、项目支持平台、项目语言、代码分析等，想得到这些徽章的话，知道自己该做些什么了吧。


### Github 彩带

在你的 [GITHUB PAGE](https://pages.github.com) 上可以添加 [Github 彩带](https://github.blog/2008-12-19-github-ribbons/)，或者 [Github Corner](http://tholman.com/github-corners/)

## GIT COMMIT

如果你的用户对你的项目特别感兴趣，可能他会看看你的 commit，看看你写代码的过程。如果 commit 信息里面都是 fix , add 甚至 aa,dd 这种无意义的信息，你肯定会觉得这个项目的作者不是特别的用心。

### [angular的commit规范](https://github.com/angular/angular/blob/master/CONTRIBUTING.md#-commit-message-guidelines)

当然作为项目的作者，可能有时候真的懒。。。。，为了解决这个问题，推荐大家几个工具。

- [Commitizen](https://github.com/commitizen/cz-cli) 是一个撰写合格 Commit message 的工具。
- [commitlint](https://github.com/conventional-changelog/commitlint) 用于检查 Node 项目的 Commit message 是否符合格式。

### changelog

如果你的所有 Commit 都符合 Angular 格式，那么发布新版本时， Change log 就可以用脚本自动生成，[conventional-changelog](https://github.com/conventional-changelog/conventional-changelog) 就是生成 Change log 的工具。

### 参考

- [GitHub 项目徽章的添加和设置](https://lpd-ios.github.io/2017/05/03/GitHub-Badge-Introduction/)
- [awesome-bookmarks](https://panjiachen.github.io/awesome-bookmarks/website/#开发)
- [Commit message 和 Change log 编写指南](http://www.ruanyifeng.com/blog/2016/01/commit_message_change_log.html)
