---
layout: post
title: Autohotkey 一记
category: software
---

　　Autohotkey 是一个神奇的软件。不过它的条件逻辑还真是让我困惑了好久。比如：

``` autohotkey
If 0
  F1:: MsgBox 1
If 1
  F2:: MsgBox 2
```

　　这样，按 `F1` 和 `F2` 都会弹出提示框，也就是说 0 和 1 的结果都是真！？
　　终于在[这里][1]找到答案，按键的定义不受条件逻辑的限制。再举一个例子：

``` autohotkey
exit
F3:: MsgBox 3
```

　　`exit` 会退出，但后面的按键定义仍会执行 (参见[文档][2])。
　　那么，如果要在某些情况定义一部分按键，其他情况不定义的话，就必须用“上下文热键”：

``` autohotkey
#If A_ComputerName == ASUS
  F1:: MsgBox 1
#If A_ComputerName == DELL
  F2:: MsgBox 2
#If
```

　　这样，在两台电脑上分别仅定义了一个键。这样做的缺点是条件不能嵌套，上面的例子，第三行相当于 `else if`，最后一行相当于 `endif`。

[1]: http://ahk.5d6d.net/thread-603-1-1.html#post_3778 "if条件判断的困惑 - AutoHotkey 中文论坛"
[2]: http://www.autohotkey.com/docs/commands/Exit.htm "AutoHotkey Documentation"
