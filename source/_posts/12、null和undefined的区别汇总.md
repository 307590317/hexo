---
title: 12、null和undefined的区别汇总
categories: null和undefined
toc: true
---

# 空字符串和0 与 null和undefined的区别：
null和undefined相比于0和空字符串占用的内存更少
## null:
空对象指针->没有具体的值->一般都是我们手动设置初始值为null，后期会给其赋值；
## undefined:
未定义->连东西都没有->一般都是浏览器默认的值；
## 用null的几种情况：
1，设定变量初始值：我们设定一个变量，后期我们要使用，那么我们设置默认值为null
2，释放堆内存：在JS内存释放中，我们想释放一个堆内存，就让其值变为null即可
3，在DOM元素获取中，不存在则结果为null：我们通过DOM中提供的属性和方法获取页面中的某一个元素标签，如果这个标签不存在，获取的结果是null，而不是undefined
4，在正则的exec/字符串的match捕获中，如果当前要捕获的字符串和正则不匹配的话，捕获到的结果为null；

## 用undefined的集中情况：
1，JS预解释的时候只声明未定义，默认值为undefined；
2，在一个函数中，如果没有写return，或者return后什么都没写，默认返回值为undefined；
3，函数中设置了形参，但是执行的时候如果没有传递参数值，那么形参默认值为undefined；
4，获取一个对象的属性名对应属性值的时候，如果这个属性名不存在的话属性值为undefined；
5，严格模式下，this前没有明确的执行主体，this就是undefined
6，用来检测浏览器兼容问题，不兼容的话返回undefined
