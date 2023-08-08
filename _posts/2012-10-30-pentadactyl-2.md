---
layout: post
title: Pentadactyl 的 jQuery 集成
category: software
---

　　[上文][1]提到我试图用 Pentadactyl 脚本取代 Scriptish 扩展。标准 jQuery 库无法使用，只能用插件里的一个奇怪的 [jQuery 集成][2]。
　　首先，它提供了 `$w`、`$d`、`$b` 三个简写，分别表示 `window`、`document`、`body` *对象*，而并不是 jQuery 包装集。由于 `$` 和 `document` 在 Pentadactyl 的环境下有歧义，所以一句 `$(document)` 要在 Pentadactyl 脚本中运行，最好写成 `userContext.$($d)`。

# 页面按键响应
　　在标准 jQuery 中，可以这样写：

``` javascript
$(window).keydown(function(e) {
  // onkeydown event handle
});
```

　　而在 Pentadactyl 中这样写就会出现问题，按键成了全局的了，在某个标签页应用了该代码，所有的标签页都会响应该按键。将 `window` 改成 `content.window` 也不行，必须用 `$d`，也就是 `document`：

``` javascript
$($d).keydown(function(e) {
  // onkeydown event handle
});
```

# AJAX
　　许多用户脚本会使用 AJAX 来实现“自动加载后一页”的功能。对应的 jQuery 语句是 `$.load()`。如，要将本站标题加载到你的网页中，可以这样写：

``` javascript
$('<div>').load('{{ site.url }} .site-title').appendTo('body');
```

　　意思是，将该页面中 CLASS 为 `site-title` 的元素添加到 `<body>` 的尾部。这样的语句在 Pentadactyl 中会导致*两*个错误。
　　一是，只能加载整个页面，即只能 `load('{{ site.url }}')`。如果后面加了“空格、选择器”，那么只会有一个空的 `<div>` 被添加到尾部，没有内容。
　　另外，`load()` 的 callback 参数也不起效，不能在 AJAX 请求完成后进行其他操作。
　　二是，`appendTo()` 的参数不能是选择器，必须是包装集。即，不能用 `appendTo('body')` 而要用 `appendTo($($b))`；不能用 `appendTo('div.post')` 而要用 `appendTo($('div.post'))`。

# 简写
　　每次都用 `userContext.$`、`content.window` 很麻烦？偷个懒吧：

``` javascript
(function($, window) {
  // you can use $('p')
})(userContext.$, content.window);
```

[1]: /pentadactyl/ "Pentadactyl 使用小记"
[2]: http://5digits.org/pentadactyl/plugins#jQuery-plugin "jQuery integration"
