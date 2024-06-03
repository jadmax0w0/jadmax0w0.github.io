---
title: Hexo 设置显示 LaTeX 数学公式
date: 2024-06-03 23:55:57
tags:
  - hexo
  - latex
---

Hexo 默认使用的是 `hexo-renderer-marked` 渲染器将 Markdown 文件渲染到网页上，但是这款渲染器只支持最基本的 Markdown 功能，不支持使用 LaTeX 书写数学公式或者启用其他的渲染插件等操作。

> 关于 Hexo 的几款渲染器对比，详见 [Hexo的多种Markdown渲染器对比分析 (bugwz.com)](https://bugwz.com/2019/09/17/hexo-markdown-renderer)

如果要显示数学公式，可以选用支持 KaTeX 的 `hexo-renderer-markdown-it-plus` 渲染器。

## 安装新渲染器

首先卸载 Hexo 原有的渲染器：

```shell
npm uninstall hexo-renderer-marked
```

出现 `removed xx packages, ...` 提示，便代表卸载成功；若出现 `up to date, ...` 提示，说明已经卸载过了或者当前 Hexo 并没有使用此渲染器。

检测是否成功卸载 Hexo 的渲染器，可以紧接着运行 Hexo 三件套：

```shell
hexo clean
hexo g
hexo s
```

如果你的博客主页上每一条博客的摘要并没有直接渲染对应的 Markdown 文件的话，此时进入博客主页应该没问题。比如我的主页上每一条博客的摘要并不包含 Markdown 格式，只是提取的纯文本：

![img](https://api2.mubu.com/v3/document_image/20708248_2bf49e8f-ff3e-4509-e7ec-2958a74804e2.png)

但如果点进任何一条博客，便会报出类似 `GET` 指令失败的错误，整个网页只有一行白底黑字的错误信息。此时说明渲染器已经卸载完成。

随后，输入下面的指令安装新的渲染器：

```shell
npm install hexo-renderer-markdown-it-plus --save
```

提示 `added xx packages, ...`，安装完成。

但此时直接 `hexo clean, g, s` 三件套之后刷新网页，发现公式渲染得一塌糊涂：

![img](https://api2.mubu.com/v3/document_image/20708248_ff8d7bde-890c-4d96-ce01-4e5f28254ab8.png)

根据 `hexo-renderer-markdown-it-plus` 的 [GitHub 主页](https://github.com/CHENXCHEN/hexo-renderer-markdown-it-plus?tab=readme-ov-file) 提示，这款渲染器虽然默认启用了 KaTeX 渲染组件，但网页仍需要对应的 CSS 样式表才能正常渲染公式。

## 配置 CSS 样式表

首先复制好下面这一行：

```html
<link rel="stylesheet" href="https://cdn.bootcdn.net/ajax/libs/KaTeX/0.12.0/katex.min.css">
```

> P.S. 项目 GitHub 上使用的是 `https://cdnjs.cloudflare.com/ajax/libs/KaTeX/0.9.0/katex.min.css` 这一款样式表，中间的版本号显示的是 `0.9.0`；如果检查这款渲染器所使用的 KaTeX 组件的 [项目网站](https://www.npmjs.com/package/@iktakahiro/markdown-it-katex)，会发现中间的版本号是 `0.11.1`。但我实际用下来，发现这两个版本在我目前所用主题上多多少少有点问题，比如公式本来的斜体字母显示的是非斜体，看起来十分奇怪：
>
> ![img](https://api2.mubu.com/v3/document_image/20708248_1e637bc5-1159-494a-ea2f-c529a2fe6411.png)
>
> 尝试版本号 `0.12.0` 之后，才终于正确渲染。

然后找到你当前使用的主题目录，方法如下：

- 如果你是直接下载的主题压缩包，然后解压到了 `theme` 文件夹中，那么直接在里面找到主题文件夹打开即可；
- 如果你是通过 npm 的方式自动安装的主题，那么需要切换到 `node_modules/` 文件夹下，(一般来说) 找到 `hexo-theme-` 开头的文件夹，后面对应你现在所用主题的名字，打开该文件夹即可。

在主题目录下，打开 `layout` 文件夹，会发现里面有不少 `*.ejs` 文件。用代码编辑器或者记事本直接打开即可；会发现其内容大致便是 HTML 脚本。

在这些 ejs 文件里多打开找找，找到带有 `<head>` 标签的一个 (比如我所用的 ZenMind 主题，带有该标签的 ejs 文件在 `layout/_partial/head.ejs`)，在 `</head>` 前面粘贴上之前那一行脚本：

```html
<link rel="stylesheet" href="https://cdn.bootcdn.net/ajax/libs/KaTeX/0.12.0/katex.min.css">
```

保存后回到终端，继续 Hexo clean, generate, server 三件套，此时回到带有公式的页面，便发现已经可以完美渲染了：

![img](https://api2.mubu.com/v3/document_image/20708248_c8b18738-123b-40c6-9605-748df5bd42a6.png)
