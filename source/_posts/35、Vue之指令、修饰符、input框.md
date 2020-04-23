---
title: 35、Vue之指令、修饰符、input框
categories: VUE
toc: true
---
# vue中的指令
dom元素的行间属性，vue提供了内置的指令，必须以v-开头，后面的值均为变量
## <font color=red>**v-cloak**</font>：
消除页面刚加载时会看到{ {} }闪一下的效果，可加给最外层的根元素；
```javascript
	//需要添加对应的css样式
	[v-cloak]{
      display: none;
    }
```
## <font color=red>**v-model（表单元素设置了之后会忽略掉value，checked，selected）**</font>
让表单元素和数据实现双向绑定（映射关系）
```javascript
	<input type="text" v-model="msg">
```
## <font color=red>**v-text**</font>：代替 { {} } 渲染成文本（不会识别html标签）可以防止网速卡慢时{ {} }出现在页面上
{ {} } 中的值都会解析成文本内容；
```javascript
{{msg}}等价于<div v-text="msg"><!--此处不能再写内容,防止出现闪的效果--></div>
```
## <font color=red>**v-html**</font>：把html字符渲染成html
```javascript
	<div v-html="msg"></div>
```
## <font color=red>**v-once**</font>：只绑定一次(不能写成v-once="msg")
```javascript
	<div v-once>{{msg}}</div>
```
## <font color=red>**v-bind**</font>：动态地绑定一个或多个特性
```javascript
// 绑定一个属性
<img v-bind:src="imageSrc">
//可缩写为如下形式，此时:src后面的值就可以是变量、表达式、赋值运算、计算、三元运算符（尽量少写逻辑运算）
<img :src="imageSrc">//此时imageSrc就代表一个变量
```
## <font color=red>**v-for**</font>：遍历数组、对象、字符串、数字
解决了循环问题，更高效，会在原有的基础上进行修改，会复用原有的结构，不会修改所有DOM
```javascript
	要循环创建哪一个标签就在那一个标签上加v-for，后面的循环最好用in，用of有时会报错循环数组或对象使用v-for指令
	<div v-for="(val,index) in msg">
			{{val,index}}//msg为数组时，val为数组中的每一项，index为索引
	</div>
	<div v-for="(value,key,index) in msg">
		{{key}}={{value}}{{index}};//msg为对象时，key为对象中的每个属性名,value为属性值,index为索引
	</div>
```
## 条件渲染：<font color=red>**v-if  v-else-if v-else**</font>与<font color=red>**v-show**</font>
<font color=red>**v-if  v-else-if v-else**</font>：条件满足才渲染v-if所在标签以及标签内的内容；（操作的是DOM结构）
- 设置条件判断的DOM元素必须是连续的
- 操作的是DOM元素
- key 管理复用的元素 若不想复用DOM元素,只需要给相应的DOM元素增加不同的key值
- 频繁的显示和隐藏用v-if会很浪费性能(操作的是DOM结构)，此时要用v-show(操作的是CSS样式)

```javascript
	<div id="app">
	    <button @click="flag=!flag">请点击</button>
	//template标签是vue提供给我们的没有任何实际意义，用来包裹元素用的（v-show不支持template标签）
	    <template v-if="flag">
	        <label>登录</label>
	        <input type="text" key="1">//key 管理复用的元素 若不想复用DOM元素,只需要给相应的DOM元素增加不同的key值
	    </template>
	    <template v-else>
	        <label>注册</label>
	        <input type="text" key="2">
	    </template>
	</div>
	let vm=new Vue({
        el:'#app',
        data:{
            flag:true
        }
    });
```
## <font color=red>**v-show**</font>
条件满足才让v-show所在标签以及标签内的内容显示（操作的是元素的css样式）
```javascript
 <input type="text" v-show="flag">//flag为true时，input框才显示
```
## <font color=red>**v-on**</font>：监听 DOM 事件(v-on:click可缩写为@click)
可以用 v-on 指令监听 DOM 事件，并在触发时运行一些 JavaScript 代码（在 `methods` 对象中定义的方法，而且methods中的方法名不能和data中的变量名一样）
```javascript
	<div v-on:click ="fn1('zhaosi,$event')">{{msg}}</div>
	<div @click ="fn1('zhaosi,$event')">{{msg}}</div>//上面的简写
	方法后面可加（）可不加。不加的话默认会传入事件对象e，
	加（）代表要传参数，如果要用事件对象，则需要传$event
```
## <font color=red>**自定义指令**</font>
可通过在vue实例的<font color=red>**directives：{}**</font>属性中赋予自定义指令意义
```javascript
	<div id="app">
	   <button v-color="flag" @click="flag='blue'">变色</button>
	</div>
	let vm=new Vue({
	   el:'#app',
	   data:{
	       flag:'red'
	   },
	   directives:{//在这里赋予对应自定义指令意义
		   //可直接写对应的指令不用写v-
		   color(el,val){
		   //el和val都是默认给的：el指的是指令所绑定的DOM元素，val是一个对象，里面存储的是有关指令的所有信息，只能用val.value获取到指令绑定的变量（或者表达式）所代表的值，这里就是flag所代表的值 'red'
		   el.style.background=val.value;
		   }
	   }
	});
```
# vue中的修饰符
## 表单修饰符
**`v-model.number`**：将用户的输入值转为数值类型
**`v-model.lazy`**：在表单触发change事件时更新数据，而非触发input事件时更新数据
**`v-model.trim`**：自动过滤用户输入的首尾空白字符

## 事件修饰符
**` @click.stop`**：阻止点击事件的传播（往上传播和往下传播都会被阻止）
**` @submit.prevent`**：阻止点击提交按钮时的默认行为（阻止事件的默认行为）
**` @click.capture`**：点击的时候让事件在捕获阶段执行；
**` @click.once`**：只在第一次点击的时候让绑定的事件执行；
**` @click.self`**：只有点击的事件源是自己的时候触发事件；（判断事件源）
```
# vue中如何实现数据的双向绑定
```javascript
    let  obj = {name:'zhufeng',age:9};//数据
    let temp = {name:"lily"};//借助中间对象
    let input1 = document.getElementById("box2");//视图
    //对某一个对象使用了Object.defineProperty方法之后就要写对应的get和set方法了，不然无法像操作普通对象一样访问或者设置它的属性
    //此方法不兼容IE8及以下
    Object.defineProperty(obj,"name",{
      configurable:true,//属性是否可删除
      writable:false,//属性是否可修改
      enumerable:false,//属性是否可枚举
      get(){//获取obj的属性名对应的属性值时会调用该方法
        /*2*/ return temp['name'];
      },
      set(val){//设置obj的属性名对应的属性值时会调用此方法

        //实现视图变化数据跟着变：分两步，上面get中的为第二步（即再次读取的时候会调用get方法得到之前设置的值，以此来实现动态改变）
        //由于直接写obj.name = this.value;会导致循环调用set方法，所以要借助中间对象的形式把值赋给中间对象，获取obj.name的时候我们获取中间对象的最新值即可
        /*1、*/ temp.name=val;

        //实现数据变化视图改变
        input1.value=val;
      }
    });
    //为了初始化的时候让视图中（文本框中）有值：出现obj.name说明要访问这个属性就会用到defineProperty中的get方法
    input1.value=obj.name;
    //实现视图改变数据跟着改变
    input1.addEventListener("input",function(){
      obj.name = this.value;//当值变化时会调用set方法
    },false);
```
# 表单元素双向数据绑定（双向同步）
使用v-model属性可让input中的内容和数据实现双向同步，但是使用了v-model属性之后，input的value、checked、select属性都会失效
```javascript
<div id="app">
    <input type="text" v-model="msg">
</div>
<script src="vue.js"></script>
<script>
    let vm=new Vue({
        el:'#app',//vue的属性和方法对哪个DOM根元素起作用（对它的后代元素也起作用）
        //数据
    如果data中属性的值是一个数组，如果想改变数组中内容，只能使用原生的会修改原数组的方法进行修改，普通的 数组[索引]=值 不行；
        data:{//写在data中的属性都会挂载到当前实例上
            msg:'zhufeng'
        }
    });
    console.log(vm.msg);//'zhufeng'
</script>
```
## 单选框
对于单选框和复选框来说，加了v-mdel属性后，初始设置的value属性不会失效；
```javascript
	 <div id="app">
		<input type="radio" v-model="msg" value="男">男
		<input type="radio" v-model="msg" value="女">女
		{{msg}}
	 </div>
	let app3=new Vue({
	   el:'#app',
	   data:{
	     msg:'男'//默认选中的项，值与value的值对应，点击女的时候msg就会动态改为女
	   }
	 });
```
## 复选框
 单个复选框：
```javascript
	<div id="app">
	//复选框中加了v-model后value依然可用
		<input type="checkbox" v-model="msg">
	</div>
	let app3=new Vue({
        el:'#app',
        data:{
        //如果是复选框且只有一个的时候，会把msg的值转化为布尔值，如果为true，代表选中
            msg:false//为false时代表默认不选中
        }
    });
```
多个复选框，需要绑定到同一个数组，而且要给input设置初始value值，为了在后面选中的时候获取到对应的选中框的值
```javascript
	<div id="app">
	  //复选框中加了v-model后value依然可用
		<input type="checkbox" v-model="msg" value="吃饭">吃饭
	    <input type="checkbox" v-model="msg" value="睡觉">睡觉
	    <input type="checkbox" v-model="msg" value="敲代码">敲代码
	    {{msg}}
    </div>
		new Vue({
		  el: '#app',
		  data: {
		    msg: [] //此处的msg需要是一个数组
		  }
		})
```
## 下拉框
```javascript
	<div id="app">
	//加了multiple后就变为多选框，用ctrl+左键点击多选，msg也要换为一个数组
		<select v-model="msg" multiple>
		//加了disabled时候用户就不能选中此项了
		//option中的value是给写代码的人看的
	        <option value="0" disabled>请选择地区</option>
	        <option value="1">北京</option>
	        <option value="2">上海</option>
	        <option value="3">浙江</option>
	    </select>
	    {{msg}}//如果option的value属性不写，则取的是option中的内容
	</div>
    let app3=new Vue({
        el:'#app',
        data:{
            msg:'0'//默认选中的某一项，值与每一个option的value值对应
        }
    });
```

