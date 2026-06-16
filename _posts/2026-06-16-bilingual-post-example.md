---
layout:      post
title:       "双语文章示例"
title_zh:    "双语文章示例"
title_en:    "Bilingual Post Example"
subtitle_zh: "演示中英文随浏览器语言自动切换"
subtitle_en: "Auto zh/en switching by browser language"
excerpt_zh:  "一篇演示文章，展示如何用 i18n-zh / i18n-en 约定撰写中英双语博文。"
excerpt_en:  "A demo post showing how to write bilingual articles with the i18n-zh / i18n-en convention."
date:        2026-06-16
author:      姚工
header-img:  "img/post-bg-debug.png"
catalog:     false
tags:
    - 示例
    - i18n
---

<!--
  双语文章撰写约定 / Bilingual authoring convention
  ----------------------------------------------------------------------
  1) Front matter（页头）使用成对字段，列表页与标题会自动双语：
       title_zh / title_en        文章标题
       subtitle_zh / subtitle_en  副标题
       excerpt_zh / excerpt_en    首页列表摘要
  2) 正文用两个并排的语言块，全站会按浏览器语言/手动切换自动显隐：
       <div class="i18n-zh" markdown="1"> ...中文 Markdown... </div>
       <div class="i18n-en" markdown="1"> ...English Markdown... </div>
     注意：必须加 markdown="1"，块内才会按 Markdown 解析。
  3) 只写一种语言也可以——不加语言块即可，两种语言都会显示同样内容。
-->

<div class="i18n-zh" markdown="1">

## 这是什么

这是一篇**双语文章示例**。页面会根据你的浏览器语言自动显示中文或英文，你也可以用导航栏右侧的语言按钮手动切换，选择会被记住。

## 怎么写

把同一段内容分别放进两个语言块即可：

- `i18n-zh` 包裹中文
- `i18n-en` 包裹英文
- 两个块都加 `markdown="1"`，块内就能正常使用 Markdown（列表、代码、引用等）

> 提示：列表标题与摘要请用页头的 `*_zh` / `*_en` 字段，首页列表才能正确双语。

## 代码也照常工作

```bash
echo "hello, 你好"
```

就这么简单，开始写你的双语博客吧。

</div>

<div class="i18n-en" markdown="1">

## What is this

This is a **bilingual post example**. The page shows Chinese or English based on your browser language, and you can switch manually with the language button in the navbar — your choice is remembered.

## How to write

Put the same content into two language blocks:

- wrap Chinese in `i18n-zh`
- wrap English in `i18n-en`
- add `markdown="1"` to both blocks so Markdown (lists, code, quotes, etc.) works inside

> Tip: use the `*_zh` / `*_en` front matter fields for the title and excerpt so the home listing renders bilingually.

## Code works too

```bash
echo "hello, 你好"
```

That's it — go write your bilingual blog.

</div>
