---
title: 16、OOP(面向对象编程思想)
categories: 面向对象与继承
toc: true
---

# 单例模式
我们把对象数据类型实现`把描述同一件事务的属性或者特征归纳汇总在一起，以此避免全局变量冲突问题`的方式和思想叫做：单例设计模式；
把描述同一件事务的属性或者方法存放在某一个命名空间下，多个命名空间中的属性和方法是互不干扰的
```javascript
//=>单例模式
//1、singleton不仅仅是对象名了，在单例模式中，singleton称之为 “命名空间(nameSpace)”
var singleton={
    xxx:xxx,
    ...
};

var singleton={
    nameSpace1:{
        xxx:xxx,
        ...
    },
    nameSpace2:{
        xxx:xxx,
        ...
    }
    ...
};
```
## 使用单例模式实现模块化开发
模块化开发：把一个复杂页面按照具体功能划分成几大块，然后由不同的人分别去开发，这种模块划分的思想就是模块化开发功能。
```javascript
//=>项目主管（开发人员）：公共模块
var utils={
    trim:function(){}
};

//=>陆永勇：搜索模块
var searchModel={
    submit:function(){
        utils.trim();
    },
    ...
};

//=>唐元帅：天气模块
var weatherModel={
    setWeather:function(){},
    ...
};
//=>陈金广：频道模块
var channelModel={
    show:function(){
       //=>在当前的命名空间下调取其它命名空间的方法：指定好对应的命名空间名字即可，使用 [NameSpace].[property] 就可以操作了
        searchModel.submit();

        //=>调取本模块中的一些方法，可以直接使用THIS处理即可：此方法中的THIS一般都是当前模块的命名空间
        this.setChannel();
    },
    setChannel:function(){},
    ...
};
channelModel.show();
```
# 高级单例模式
基于JS高阶编程技巧`惰性思想`，来实现的单例模式，并且可以把一些常用的设计模式（如：命令模式、发布订阅模式、promise设计模式等）融合进来，最后清晰的规划我们的业务逻辑代码，方便后期二次开发和维护，这种设计思想综合体就是高级单例模式，也是最常用的。
```javascript
var serchModel=(function(){
	function submit(){
	}
	return {
		init:function(){
			this.submit();
		}
	}
	})();
	searchModel.init();
```
# 对象、类、实例
对象：万物皆对象
类：对象的具体细分（按照属性或特性细分的一些类别）
实例：某一个类中的具体事物
# JS常用的内置类
## 数据类型的类
Number：每个数字或者NaN是它的一个实例
String：字符串类
Boolean：布尔类
Null
Undefined：浏览器屏蔽了我们操作null和udnefined这个类
Object：对象类，每个对象数据类型都是它的实例
 - Array：数组类
 - RegExp：正则类
 - Date：日期类

Function：函数类，每个函数都是它的一个实例
## 元素对象或者元素集合的类
HTMLCollection：元素集合类
 + getElementsByTagName()
 + getElementsByClassName()
 + querySelectorAll

NodeList：节点集合类
 + childNodes
 + getElementsByName()

HTMLDivElement
HTMLElement
Element(标签类)
Node(节点类，Element只是其中的一个元素节点)

![](http://ww1.sinaimg.cn/large/0067rNTTly1fpmvbxlb9tj30w60as3yw.jpg)

## 为什么getElementById的上下文只能是document？(即getElementById为什么只能通过document来调用)？
因为只有在Document这个类上才有getElementById这个方法，其他类上(如：HTMLDivElement类)没有getElementById这个方法，而document是HTMLDocument这个类的一个实例,能通过document.\__proto\__.\__proto\__找到Document这个类的原型上公有的getElementById方法。
![](http://ww1.sinaimg.cn/large/0067rNTTly1fpmvcf60cwj30ph0fawf1.jpg)

# 基于面向对象创建数据
## `创建方式：两种`
### 字面量创建方式
- var  obj={}；

### 实例创建方式（构造函数方式）
- var obj=new Array();

1、对于引用数据类型来说，两种创建方式是大致相同的，只不过，两种方法创建的语法不同。
```javascript
两种创建方式在核心意义上没有差别，都是创建Array这个类的一个实例，但是在语法上是有区别的
1、字面量创建方式传递进来什么，都是给数组每一项加入的内容
2、构造函数创建方式
new Array(10)：创建一个长度为10的数组,数组中的每一项都是空
new Array('10')：如果只传递一个实参，并且实参不是数字，相当于把当前值作为数组的第一项存储进来
new Array(10,20,30)：如果传递多个实参，不是设置长度，而是把传递的内容当做数组中的每一项存储起来
```
2、对于基本数据类型来说，字面量方式创建出来的结果和实例方式创建出来的结果是有一定区别的，从严格意义上来讲，只有实例创建出来的结果才是标准的对象，数据类型值也是标准的基本数据类型，也是标准的内置类的实例；对于字面量方式创建出来的结果是基本数据类型的值，不是严格的实例，但是由于JS的松散特点，导致了可以使用 内置类.prototype上提供的方法；




