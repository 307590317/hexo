---
title: 14、DOM的映射、重绘、回流
categories: DOM操作
toc: true
---
<font color=red>操作真实的DOM结构是比较消耗性能的(尽量减少)</font>
# DOM的映射机制
浏览器在渲染页面的时候，给每一个元素都设置很多内置的属性(包含样式的)，当我们在JS中把堆内存中的某些东西修改了，大部分情况下，浏览器都会检测到你的修改，按照最新的修改的值重新渲染页面中的元素。
# DOM的重绘和回流
## 重绘（repaint）针对某一个元素
当元素的样式发生改变(不修改元素位置的样式)，浏览器会把当前元素重新的进行渲染(DOM性能消耗低)
 触发重绘的场景：
 - color的修改
 - text-decoration的修改
 - background的修改
 - a:hover也会造成重绘
 - :hover引起的颜色等不导致回流的style变动
## 回流(reflow)针对整个页面
当元素的位置发生改变，浏览器会把整个页面的DOM结构进行重新计算，计算出所有元素的最新位置，然后再渲染(DOM性能消耗非常大)
 - 1、width/height/border/margin/padding的修改
 - 2、动画，：hover等伪类引起的元素表现改动，display=none等造成页面回流
 - 3、appendChild等DOM元素操作
 - 4、font类style的修改
 - 5、background的修改，部分background的修改只触发重绘，IE不用考虑
 - 6、scroll页面，不可避免
 - 7、resize页面，桌面版本进行浏览器大小的缩放。
 - 8、读取元素属性（offsetLeft、offsetTop、offsetHeight、offsetWidth、scrollTop/Left/Width/Height、clientTop/Left/Width/Height、getComputedStyle()、currentStyle(in IE))；
## 如何解决重绘和回流
如何避免：
尽可能在 DOM 末梢通过改变 class 来修改元素的 style 属性：尽可能的减少受影响的 DOM 元素。

避免设置多项内联样式：使用常用的 class 的方式进行设置样式，以避免设置样式时访问 DOM 的低效率。

设置动画元素 position 属性为 fixed 或者 absolute：由于当前元素从 DOM 流中独立出来，因此受影响的只有当前元素，元素 repaint。

牺牲平滑度满足性能：动画精度太强，会造成更多次的 repaint/reflow，牺牲精度，能满足性能的损耗，获取性能和平滑度的平衡。

避免使用 table 进行布局：table 的每个元素的大小以及内容的改动，都会导致整个 table 进行重新计算，造成大幅度的 repaint 或者 reflow。改用 div 则可以进行针对性的 repaint 和避免不必要的 reflow。

避免在 CSS 中使用运算式：学习 CSS 的时候就知道，这个应该避免，不应该加深到这一层再去了解，因为这个的后果确实非常严重，一旦存在动画性的 repaint/reflow，那么每一帧动画都会进行计算，性能消耗不容小觑。
