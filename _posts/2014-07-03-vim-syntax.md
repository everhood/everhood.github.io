---
layout: post
title: Vim 高亮的困惑
category: Vim
---

　　平时我们定义语法高亮，可以简单地使用

``` vim
:syn match StatusLine " "
```

　　这样可以把所有的空格都高亮为缺省的高亮组 `StatusLine`。
　　不过，这未必总是奏效。比如 XML 语法文件

``` xml
<a>
  <b/>
</a>
```

　　对这个文件执行上述高亮命令，`<b/>` 前的空格并没有高亮。
　　这个令人困扰的问题的官方解释是：

```
:h :syn-priority
1. When multiple Match or Region items start in the same position, the item
   defined last has priority.
2. A Keyword has priority over Match and Region items.
3. An item that starts in an earlier position has priority over items that
   start in later positions.
```
　　这里第 3 点大意是说，`<b/>` 前面的空格属于更早的区域，即从 `<a>` 开始一直到 `</a>` 都属于一个高亮区域 (可以在系统的 `syntax/xml.vim` 中看到定义)，所以优先级更高。
　　但是第 1 点说，最后定义的优先级更高啊？第 2 点也说，Keyword 比 Region 的优先级更高啊？总之这算是 Vim 的一个 bug 吧。怎么解决呢？
　　我们知道 [Indent Guides][1] 插件对所有的语法文件都能高亮辅助线。它的代码简单地用了 `matchadd` 函数。所以上面的 `:syn` 语句应当改为

``` vim
au FileType * call matchadd("StatusLine", '\(\s\|\u3000\)\+$')
```

　　这样可以把行尾多余的空格 (半角、全角) 高亮出来。注意，第二个参数必须用单引号 `'` 不能用双引号 `"`，因为其中有正则表达式。
　　也不能用 `au BufReadPost`，因为它早于 `FileType`，会被后者覆盖。

[1]: http://www.vim.org/scripts/script.php?script_id=3361 "Indent Guides - A plugin for visually displaying indent levels in Vim."
