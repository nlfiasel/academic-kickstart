+++
title = "Emacs ＋ ox-hugo 配置总结"
categories = ["Emacs"]
draft = false
+++

在配置的过程中, 用到的参考文献大致如下:

-   [使用Emacs和Hugo academic主题](https://zlearning.netlify.com/linux/emacs/emacs-hugo-academic.html)
-   [使用 Emacs + ox-hugo 来写博客](http://blog.jiayuanzhang.com/post/blog-with-ox-hugo/)
-   [博客写作流程之工具篇： emacs, orgmode, hugo & ox-hugo](https://www.xianmin.org/post/ox-hugo/)

为了简单起见, 选择了 academic 这个主题作为配置的基础, 使用 netlify 作为博客的服务提供者, 其实 [就是这个一键安装脚本](https://sourcethemes.com/academic/docs/install/#install-with-web-browser) 啦!

然后根据 [Getting started](https://sourcethemes.com/academic/docs/get-started/) 的简单教程, 进行一些简单地配置就可以初步使用了. 因为是第一次使用基于 hugo 的博客, 所以使用一个成套的可以拿来就用的可以有效降低学习成本.
大致了解过后也可以为之后的进一步的配置打下基础.

之后就是使用 Emacs 结合 ox-hugo 进行文章的管理了, 文章的管理支持将多篇文章放置于同一个 org 文件下进行管理, 也可以采用多个文件进行管理, 基于个人习惯, 我这里使用一个 org 文件管理全部的文章. 那么, 假设我的博客文件夹位于=~/blog/= 目录下, 并且文章的 md 文件放置于 `~/blog/content/post/` 目录, 那么只需要在 org 文件的开头设定

```org
#+SEQ_TODO: TODO DRAFT DONE
#+HUGO_BASE_DIR: ~/blog
#+HUGO_SECTION: post
```

对于每一篇文章可以使用, 类似于下面的方式指定生成的文档的名字, 便于进行分类(不过也是可以采用自动命名的):

```org
:PROPERTIES:
:EXPORT_FILE_NAME: emacs+hugo
:END:
```

然后采用 org 的格式对文章进行维护就可以了, 这里推荐可以将所有的 org 文档统一采用
git 进行管理, 这样在一定程度上可以防止误删, 或者是可以放心的删除认为不重要的内容...

最后提供一份简化的 org 文档示例作为参照:

PS: 在 org 文档中添加 org 的示例, 可以将使用 org 语法的行以 `,` 开始, 阻止 org-mode
将示例中的内容视作一般的 org 文档, 并且在 Export 导出的时候, 会自动地将 `,` 删去,
关于 org 与此相关的详细用法, 可以参照 org-guide 的 Working with Source Code 一章

```text
#+SEQ_TODO: TODO DRAFT DONE
#+HUGO_BASE_DIR: ~/blog
#+HUGO_SECTION: post

* Linux :@Linux:
** DONE Xkeysnail 安利
:PROPERTIES:
:EXPORT_FILE_NAME: xkeysnail-recommand
:END:
吃我一记安利系列...
```

未完待续...
