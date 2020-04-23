---
title: 35、Vue之生命周期
categories: VUE
toc: true
---
# 生命周期与钩子函数
生命周期：
<font color=red>**beforeCreate**</font>：el 和 data 并未初始化 （此方法不常用）
<font color=red>**created**</font>：执行此方法时已经，完成了 data 数据的初始化，el的初始化未完成。用来发送ajax

<font color=red>**beforeMount**</font>：（执行此方法时已经完成了 el 和 data 初始化 （已经赋予了对应的值））
渲染DOM之前先确认下是否有要编译的根元素（有无el属性），有才继续确认是否具有模板属性`template`,如果有模版属性，则会用`template`的值替换掉HTML中的结构，`template`模版中只能有一个根元素（而且不能是文本）；
<font color=red>**mounted**</font>：（执行此方法时代表已经挂载结束了）
把编译好的数据挂载到DOM元素上，最后渲染成真实的DOM元素；真实DOM已经渲染完成，可以操作DOM了

<font color=red>**beforeUpdate**</font>：当页面依赖的数据更改之后触发（此时DOM结构还没有重新加载）
<font color=red>**updated**</font>：DOM结构重新加载之后触发

调用vm.$destroy()之后触发下面两个事件：
<font color=red>**beforeDestroy**</font>：实例销毁之前调用。在这一步，实例仍然完全可用。（可在此处清除定时器，清除事件绑定）
<font color=red>**destroyed**</font>：Vue 实例销毁后调用。调用后，Vue 实例指示的所有东西都会解绑定，所有的事件监听器会被移除，所有的子实例也会被销毁。（意义不大）
# 获取数据
vue中给我们提供了一个created函数，在实例创建完成后会被立即调用。方法中的this指向的也是vue的实例
```javascript
let vm=new Vue({
     el:'#app',
     created(){
       //实例创建完成后会立即执行created方法
       this->vm这个实例
     },
     data:{
        msg:''
     }
});
```
