---
layout: post
title: 借助 Autohotkey 提升效率
category: software
---

　　Windows 的高定制性，使其一旦经过深度配置，就会比其他系统都要好用。
　　这一篇的主题是借助 [Autohotkey](https://www.autohotkey.com/) 扩展 `Win` 键和 `Caps Lock` 键。

　　`Win` + 字母键可以打开不同的程序，`Win` + 数字键可以打开任务栏相应位置的窗口。那我们就可以把常用的软件绑定到其组合键上，随时打开该软件。
　　以浏览器 (Firefox) 为例：

``` autohotkey
; Win-b
; Browser
#b::
  ; 活动窗口，循环切换其他浏览器窗口
  IfWinActive ahk_class ^MozillaWindowClass$
  {
    WinGet, winList, List, ahk_class ^MozillaWindowClass$
    If winList > 1
      WinActivate, % "ahk_id" . winList%winList%
    Return
  }
  ; 非活动窗口，激活
  IfWinExist ahk_class ^MozillaWindowClass$
  {
    WinShow
    WinActivate
    Return
  }
  ; 寻找隐藏窗口
  DetectHiddenWindows, On
  IfWinExist ahk_class ^MozillaWindowClass$
  {
    WinShow
    WinActivate
  }
  Else  ; 未启动
  {
    Run firefox.exe  ; 这里可以改成全路径
    WinWait ahk_class ^MozillaWindowClass$
    If !ErrorLevel
      WinActivate
  }
  DetectHiddenWindows, Off
  Return
```

　　如此一来，可以给每个字母绑定一个常用软件。

　　而 `Caps Lock` (以下简称 `CL`) 在键盘上位置极佳，但很少使用。许多人通过修改注册表将其与 `Ctrl` 键互换，而下面这段代码使其单击相当于 `Esc`，与其他键组合则模拟其他常用键效果。
　　以下 `CL+a` 表示按住 `CL` 单击 `a`，然后松开 `CL`，就像 `Ctrl+c` 一样。

``` autohotkey
; 单击，则模拟 Esc 键
CapsState := 0
*Capslock::
  CapsState := 1
  KeyPressed := 0
  KeyWait, Capslock
  CapsState := 0
  If !KeyPressed
    SendInput {Esc}
  Return

#If CapsState

; CL+Esc
; 模拟单击 CL 键
Esc:: SetCapsLockState % !GetKeyState("CapsLock", "T")

; aswd
; 方向键
*a:: press("{Left}")
*d:: press("{Right}")
*w:: press("{Up}")
*s:: press("{Down}")

; jkl;
; 方向键
*j:: press("{Home}")
*;:: press("{End}")
*l:: press("{PgUp}")
*k:: press("{PgDn}")

; c vbn fgh rty
; 数字键
c:: press(0)
v:: press(1)
b:: press(2)
n:: press(3)
f:: press(4)
g:: press(5)
h:: press(6)
r:: press(7)
t:: press(8)
y:: press(9)

; CL+q
; 菜单键
q:: press("{AppsKey}")


#If

; 现支持 CL、Ctrl、Alt 任意组合
press(str) {
  global KeyPressed
  GetKeyState, state, Shift
  If (state = "D")
    str := "+" . str
  GetKeyState, state, Alt
  If (state = "D")
    str := "!" . str
  GetKeyState, state, Control
  If (state = "D")
    str := "^" . str
  SendInput %str%
  KeyPressed := 1
}
```

　　本文仅是抛砖引玉，提供一种方法，每个人可以按照自己的喜好增加其他的组合键。
