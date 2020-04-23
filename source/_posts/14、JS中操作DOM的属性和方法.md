---
title: 14、JS中操作DOM的属性和方法
categories: DOM操作
toc: true
---

# 获取节点
## <font color=red>document.getElementById()</font>
在文档中通过元素的ID名来获取一个元素，只能通过document对象来调用，document属于Document类的一个实例，只有在Document类的原型上才有getElementById这个方法。
我们把document称之为上下文（context）：上文和下文，也就是获取元素时候限制的那个范围。
获取的结果是一个对象数据类型的值，在JS中使用DOM提供的方法，获取的元素都是对象，所以我们把获取的结果称之为 ‘元素对象’。
1.如果页面中的ID重复了，我们获取的结果是ID对应的第一个元素对象
2.在IE7及以下会把表单元素中的name属性值当做ID来使用；而且会忽略ID的大小写（项目中尽量不要让表单的name和其他元素的id相同）
3.我们如果把JS放在结构下面，我们可以直接使用ID值来获取这个元素（不需要通过getElementById来获取），而这种方式会把页面中所有ID为同一个的都获取到（只有一个的话获取的是元素对象，多个的话获取的是类数组集合）=>不推荐
**<font color=red>注意：不要让表单元素的name属性值和其他元素的id重复、不要用id的大小写来区分不同的元素</font>**
不通过直接操作ID值的方式如何获取多个相同ID的元素？
```javascript
var allList=document.getElementsByTagName('*');
var ary=[];
for(var i=0;i<allList.length;i++){
	allList[i].id===xxx?ary.push(allList[i]):null
}
return ary;
```
## <font color=red>context.getElementsByTagName()：</font>
在指定上下文中通过元素的标签名来获取子子孙孙元素中的一组元素，获取到的元素是类数组集合，通过索引来调用某一个
1、以数字作为属性名，每一个属性存储的都是获取到的每一个li，JS中我们把数字属性名叫做“索引”（索引是逐级递增的）
2、有一个length属性存储的是当前集合中LI的个数
具备以上的两个特点特别像数组，但是，它不是数组，所以我们把他称之为“类数组”

## <font color=red>document.getElementsByName()：
</font>在指定上下文中通过元素的name属性来获取一组元素（类数组：节点集合 NodeList）
在IE浏览器下只对表单元素起作用
这个方法应用于获取具有同样name 的表单元素

## <font color=red>context.getElementsByClassName()：
</font>通过元素的样式类名来获取（class值）一组元素，获取的也是一个类数组集合，通过索引来调用某一个；
getElementsByClassName()是项目中最常用的一种方法，但是这个方法在IE6-8下不兼容

## <font color=red>document.documentElement：</font>获取HTML元素
## <font color=red>document.body：</font>获取body元素
# 在移动端获取元素常用的方法（IE6-8下不兼容）
## document.querySelector()：
获取一个

## document.querySelectorAll()：
获取多个   类数组集合(获取到的元素集合或节点集合不存在DOM映射，因为获取到的集合不是标准的NodeList，而是属于StaticNodeList(静态集合))
document.querySelector("#tab");ID选择器
document.querySelectorAll("#tab li ");//层级选择器
document.querySelectorAll("input[type='radio']");//过虑选择器
## className:
通过这个属性可以获取或者设置当前元素对象的样式类
## innerHTML:
通过这个属性可以获取或者设置元素里面的内容
## innerText:
通过这个属性可以设置或者获取元素里面的文本内容
火狐中不支持innerText属性，我们用textContent代替innerText
<font color=red>innerHTML与innerText的区别：</font>
- innerHTML可以把增加内容中的HTML标签进行识别，innerText只能设置或者获取文本内容，不能识别HTML标签，HTML标签会被当作文本处理

## style属性：
通过这个属性我们可以获取或者设置元素的样式（只能是元素的行内样式，获取或者设置都是，写在内嵌或者外链中的css样式无法获取到）
## window.getComputStyle(元素，伪类)[属性值] || 元素.currentStyle[属性值]：
获取当前元素经过浏览器计算的属性值，不管是写在哪的；
# 获取关系的属性
## `childNodes`：获取所有的子节点
获取当前元素的所有子节点（节点集合：类数组）
注：不仅是元素子节点，文本、注释等都会包含在内；子节点说明只是在儿子辈分中查找；

## `childredn`：获取所有的元素子节点
获取所有的元素子节点（元素集合）
在IE6-8下获取的结果和标准浏览器中有区别：会把注释节点当作元素节点获取到

## `parentNode`：获取元素的父亲节点（元素对象）
## `previousSibling`：获取哥哥节点（包括文本或注释）
## `nextSibling`：获取弟弟节点（包括文本或注释）
IE6-8下不兼容
## `previousElementSibling`：获取上一个元素节点
## `nextElementSibling`：获取下一个元素节点
## `firstChild`：获取所有子节点中的第一个
## `lastChild`：获取所有子节点的最后一个
firstElementChild/lastElementChild IE6-8下不兼容
# 节点类型
| 节点类型  |    nodeType | nodeName  | nodeValue |
| :--------: | :-------: | :-----------: | :--------: |
|元素节点   |         1         |大写的标签名|     null     |
| 属性节点  |         2         |大写的属性名|属性值|
| 文本节点  |         3         |       #text     |文字内容|
| 注释节点  |         8         |   #comment |注释内容|
| document |         9         |  #document |null|
在标准浏览器下，我们把空格和回车都当成文本节点处理
ele.tagName:获取当前元素的标签名（获取的一般都是大写）,tagName只有元素节点才有；

# 节点操作
## `创建节点`
- document.createElement(元素标签):动态创建一个元素节点
- document.createAttribute(元素属性):动态创建一个属性节点
- document.createTextNode(文本内容):动态创建一个文本节点

## `插入节点`
- appendChild()：把元素添加到指定容器的末尾位置
容器.appendChild（元素）
- insertBefore(a,b)：把新的元素a，添加到老的元素b之前
容器.insertBefore（a,b）

## `替换节点`
- replaceChild(a,b)：用a元素来替换容器中的b元素
容器.replcaeChild(a,b);

## `复制节点`
- cloneNode（true/false）：克隆元素节点，true表示包括所有子节点，默认为false
要克隆的元素.cloneNode（），无法克隆元素绑定的事件

## `删除节点`
- removerChild():从容器中删除指定元素
容器.removeChild（元素）

# 属性操作
## `获取属性`
- getAttribute(元素属性名):获取元素节点中指定属性的属性值
元素节点.getAttribute（元素属性名）

## `设置属性`
- setAttribute(元素属性名):设置或者改变元素节点的属性值（一般都是操作自定义属性）
元素节点.setAttribute（属性名，属性值），可以设置自定义的属性，会修改html结构，直接在html中体现出来。用setAttribute设置的只能用getAttribute来获取，只能用removeAttribute来删除
`注意:在IE6-8下无法修改和设置class属性`

## `删除属性`
- removeAttribute(元素属性名):删除元素节点的属性
元素节点.removeAttribute（属性名）

# `设置或者获取自定义属性有xxx.属性名=属性值和 xxx.setAttribute(元素属性名)两种方式，有以下区别`：
1，xxx.属性名 结果体现在对象的属性上。与HTML结构无关。
1，xxx.setAttribute(属性名)  结果体现在DOM结构上，即在HTML结构中可以看到；
如果使用DOM的内置属性操作元素，那么元素就会被当作特殊对象，和HTML结构产生映射关系(即结果会呈现在HTML结构上)