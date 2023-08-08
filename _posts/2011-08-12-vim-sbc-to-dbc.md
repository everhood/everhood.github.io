---
layout: post
title: Vim 替换全角字符为半角字符
category: Vim
---

　　下面命令可将全角的**英文字母**和**数字**替换成半角：

``` vim
:%s`[\ua3b0-\ua3b9\ua3c1-\ua3da\ua3e1-\ua3fa]`\=nr2char(char2nr(submatch(0))-0xa380)`g
```

　　搜索串列出所有的全角字符，`\ua3b0-\ua3b9` 表示全角数字０-９，`\ua3c1-\ua3da` 表示全角字母Ａ-Ｚ，`\ua3e1-\ua3fa` 表示全角字母ａ-ｚ。
　　若 `encoding=utf-8`，则可使用：

``` vim
:%s`[\uff10-\uff19\uff21-\uff3a\uff41-\uff5a]`\=nr2char(char2nr(submatch(0))-0xfee0)`g
```

　　替换串以 `\=` 开始，可参考

``` vim
:h sub-replace-expression
```

　　`submatch(0)` 可得到完整的匹配。使用 `nr2char()` 和 `char2nr()` 进行运算。
