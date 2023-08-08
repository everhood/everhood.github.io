---
layout: post
title: Pentadactyl 使用小记
category: software
---

　　[Pentadactyl][1] 是火狐浏览器的一个扩展，可以实现类 Vim 的操作。它不仅可以让你仅使用键盘来浏览网页，更可以定制整个浏览器的界面，以及根据你的喜好来定制一系列快捷操作。
　　它在操作上使用了 Vim 的大部分按键及命令，然而在脚本上并没有使用 Vim Script，而是 JavaScript。而这个 JavaScript 对页面开发员来说可能有一些古怪，本文记录我在用 Pentadactyl 来取代 Scriptish 时所作的尝试。
　　说它古怪，举例来说，在 normal 模式下输入：

``` vim
:echo document.body
```

　　会返回 `undefined`。
　　而输入

``` vim
:echo content.document.body
```

　　则返回正确的 `<html:body>...<html:body>`，也就是 HTML 文档的 `body` 元素。这里，`document` 对象不再是全局的。
　　再举个例子：

``` vim
:source http://ajax.microsoft.com/ajax/jquery/jquery-1.7.min.js
:echo $.fn
:echo window.$.fn
```

　　你会发现，jQuery 中的 “`$`”也不能直接用了。
　　原来我使用 [Scriptish][2] (其之于 [Greasemonkey][3] 就相当于 Pentadactyl 之于 [Vimperator][4]) 来定制页面，可以在头部增加下面语句来载入外部脚本文件：

``` javascript
// @require http://ajax.microsoft.com/ajax/jquery/jquery-1.7.min.js
```

　　这样就可以在运行脚本之前，先加载 jQuery。
　　要让 Pentadactyl 也实现 Scriptish 的功能，可以用 autocmd 命令：

``` vim
:autocmd DOMLoad google.com runtime mygoogle.user.js
```

　　因为上面说的原因，`document` 和 `$` 都不再是全局对象，所以原来的 userscript 脚本就不能直接搬过来用。

# 尝试一：添加 `script` 元素
　　最简单的方法是，在载入 DOM 后在 `<head>` 中附加 2 个 `<script>` 元素，一个是 jQuery，一个是 userscript。经我尝试完全可行，可是我还安装了 [NoScript][5] 扩展，有一些网站我不允许执行网页上的脚本，只执行我自己的脚本。而如果该域名没有被允许，所有脚本都无法运行，这样一来用添加脚本元素的方法就行不通了。

# 尝试二：载入 jQuery
　　接下来尝试在启动火狐的时候，先载入 jQuery 作为全局对象，这还可以省去每次打开页面都加载一遍的浪费。将 jQuery 脚本保存到 `runtimepath` 中，在 \_pentadactylrc 中添加如下语句：

``` vim
:runtime jquery-1.7.min.js
:autocmd DOMLoad google.com runtime mygoogle.user.js
```

　　这样也不成功，因为直接载入 jQuery 源码后，甚至连 `window.$('body').size()` 都返回 0，找不到 `body` 元素。事实上，可以通过 `window.$('*')` 来看“所有元素”究竟是些什么玩意。我对 Firefox 本身并不熟悉，也没有找到相关的文档。

# 尝试三：使用 jQuery 插件
　　使用官方提供的 [jQuery 集成插件][6]，可以使用全局的 `$` 了，但是这个对象没有 `fn` 或 `prototype` 成员，不能定义 `$.fn.myfunc`。于是只能将就着这样用了。
　　在 [Pentadactyl 文档][7]中，提到了 `tab.linkedBrowser.reload();`，但这个 `linkedBrowser` 哪儿都找不到文档，看来 Pentadactyl 要像 Vim 那样广为接受，还要假以时日。

# 总结
　　任何工具都是为人民服务的，我当初听了许多“资深”程序员吹牛说 Vim 有多好，于是也玩了一把，并且玩得很深。其实，我不觉得我的效率有什么提高，我在学习 Vim 操作、定制 Vim、编写 Vim script 上花的时间已经远远超过了使用普通编辑器“也许会浪费的时间”的期望值。在我的同事中，使用 Notepad 配合 VS 来编程的牛人大有人在。我是在一个很偶然的情况下接触了 Pentadactyl，试用下来感觉还行，好在它没有改变鼠标的操作。更骨灰的玩法，我也就不再花工夫折腾了。

[1]: https://addons.mozilla.org/zh-CN/firefox/addon/pentadactyl/ "Pentadactyl :: Firefox 附加组件"
[2]: https://addons.mozilla.org/zh-CN/firefox/addon/scriptish/ "Scriptish :: Firefox 附加组件"
[3]: https://addons.mozilla.org/zh-CN/firefox/addon/greasemonkey/ "Greasemonkey (zh-CN) :: Firefox 附加组件"
[4]: https://addons.mozilla.org/zh-CN/firefox/addon/vimperator/ "Vimperator :: Firefox 附加组件"
[5]: https://addons.mozilla.org/zh-CN/firefox/addon/noscript/ "NoScript :: Firefox 附加组件"
[6]: http://5digits.org/pentadactyl/plugins#jQuery-plugin "jQuery integration"
[7]: http://5digits.org/help/pentadactyl/eval.xhtml#:javascript
