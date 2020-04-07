# 在本地调试React外部依赖的正确姿势

## 前言
实际开发中，可能我们的工程结构并不仅仅只有一个，很可能我们会专门抽出一个专用的UI库，而实际开发的工程将引用这个库。UI库虽然相对于业务工程来讲，相对稳定，修改变更很少，但是不可避免的会有修改，每次修改我们如果都要重新发布的话，相当麻烦，而且一部分私有库需要认证才能安装，如果我们暂时不知道这些信息，又要引入这个库来顺利开发，应该怎么办呢？

## Yarn link / npm link
通过 **link** 命令可以将我们要使用的外部依赖链接到全局的 `node_modules`，例如我们在 `my-ui` 工程中执行如下命令 `yarn link` 或 `npm link`，之后，我们就可以直接在 `my-app` 工程中执行 `yarn link my-ui` 来使用之前链接的 `my-ui` 本地版本。这种用法，编译是没有问题的，但是实际运行项目后会出现如下错误。

> [hooks can only be called inside the body of a function component](https://zh-hans.reactjs.org/warnings/invalid-hook-call-warning.html)

> error TS2717: Subsequent property declarations must have the same type.

这个问题究其原因是因为 `my-ui` 和 `my-app` 维持了2份 React 或其他依赖的副本，由此导致了各种奇怪问题，详情参考 [Hooks don't work with yarn link](https://github.com/facebook/react/issues/14257)。看回答官方貌似也不会解决这个问题，而只是考虑优化下错误提示。

那这个问题貌似无解，幸好下面有个哥们提到一个叫 [Yalc](https://github.com/whitecolor/yalc) 的解决方法。下面详细介绍下这个叫 `Yalc` 的东东。



## [Yalc](https://github.com/whitecolor/yalc)

是一个比 npm | yarn link 更加先进的解决方法，能够避免相关依赖的问题。

`yalc` 在本地环境中创建了一个共享的本地开发包的本地存储库。
当在指定包目录中运行 `yalc publish`时，会将本来发布到 NPM 上的相关文件放到本地一个共享的全局存储中。
当在需要引用的项目中运行 `yalc add my-package` 时，它会将包内容拉入根目录的的 .yalc 文件夹中，并将一个文件 `"file:.yalc/my-package"` 注入 `package.json`。如果使用 `yalc link my-package` 的话，就类似 `npm | yarn link` 一样创建项目链接，不会修改 `package.json`。
yalc 会在项目中创建一个特殊的 `yalc.lock` 文件（类似于yarn.lock和package-lock.json），用于确保执行 yalc 例程时的一致性。
首先执行命令 `yarn global add yalc` 全局安装 `yalc`

我们在 `my-ui` 中执行 `yalc publish`，将其发布到本地存储库中。

然后在 my-app 工程中执行 `yalc add my-ui`，这里的安装方式和 `npm | yarn` 一样，可以指定安装版本等信息，如此我们的本地存储库可以分版本管理。

最后执行 `yarn install` 来更新依赖关系。我们再重新编译执行 my-app 后就不会再抛出如下异常。

> hooks can only be called inside the body of a function component

如果 my-ui 发生了更新以后，我们可以直接在 my-ui 工程中执行 `yalc push`，它会自动的更新所有添加了 my-ui 依赖的工程，比如 my-app ，整个过程真是优雅呀。
