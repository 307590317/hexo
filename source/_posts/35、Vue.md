---
title: 35、VUE
categories: VUE
toc: true
---

渐进式框架
声明式渲染（无需关心如何实现）、组件化开发、客户端路由（vue-router）、大规模的数据状态（vuex）、构建工具（vue-cli）
全家桶：vue.js+vue-router+vuex+vue-cli+axios

# Vue核心知识点
数据驱动(主要操作的是数据)
数据变化会导致视图自动更新
组件化开发
# 框架的模式
MVC单项的数据绑定
- 只能通过改变数据来改变视图
![](http://ww1.sinaimg.cn/large/0067rNTTly1fperzkra9aj30g30bvtd2.jpg)

MVVM 双向的数据绑定
- 视图发生改变，数据也会跟着变
- 数据发生改变，视图也会跟着改变

# vue的使用方式
1、采用cdn：`<script src="https://unpkg.com/vue"></script>`
2、在需要的项目中安装vue模块 npm install vue，安装之后导入`vue.js`文件
## 跑环境
初始化package.json文件，在项目中执行npm init -y可自动生成一个默认的package.json文件；
由于项目依赖的模块较大，每次上传下载同步都很浪费时间，所以我们只需要上传package.json文件，里面写了所有的依赖模块，我们同步下来之后只需要在需要的项目中打开cmd窗口执行npm install，就可以把项目依赖的模块下载到；
# vue中数组的变异方法（能改变原数组）
如果data中属性的值是一个数组，如果想改变数组中内容，只能使用原生的会修改原数组的方法进行修改，普通的 数组[索引]=值 不行；
操作数组的方法：（以下方法都会修改原数组）
- unshift、push、shift、pop、splice、reverse、sort

vue中常用的(以下方法都为声明式方法，for循环为编程式方法，能清晰的看到每一步)
- forEach、filter、map、some、every、reduce、find(ES6)

forEach，for，for in，for of的区别
**` forEach`**：不支持返回值，只是普通的循环遍历
**`for in`**：key输出的值为字符串类型，包括把数组当成对象添加的属性也可以遍历出来
**`for of`：只返回具有数字索引的属性。这一点跟for...in循环也不一样。**（不能遍历对象）
```javascript
	let arr = [3, 5, 7];
	arr.foo = 'hello';

	for (let i in arr) {
	//for in是把arr当成对象遍历，i是属性名，包括arr的私有属性
	  console.log(i); // "0", "1", "2", "foo"
	}

	for (let i of arr) {
	//for of是把arr当成数组遍历，i是数组中的每一项
	  console.log(i); //  "3", "5", "7"
	}
	for of循环不会返回数组arr的foo属性
	如果只想拿到索引，可用keys()方法
	for (let index of ['a', 'b'].keys()) {
	  console.log(index);
	}
	// 0
	// 1
	如果两个都想拿到，可用entries()方法
	for (let (index, elem) of ['a', 'b'].entries()) {
	  console.log(index, elem);
	}
	// 0 "a"
	// 1 "b"
```
# 把Vue当成对象添加的私有方法
**`全局过滤器`**：Vue.filter(方法名,方法)；相当于给全局过滤器中增加了一个方法（一次只能增加一个）
```javascript
 Vue.filter('my',data=>{return data})；
 //页面中使用
 <div id="app">
   {{count|my}}
 </div>
```
**`创建一个全局组件`**：Vue.component(自定义标签名,需要配置的参数)
```javascript
//js代码
Vue.component('my-hand',{
        template:'<h3>{{msg}}</h3>',
        data(){
            return {msg:'我很英俊'}
        }
    });
 let vm=new Vue({
    el:'#app'
 })
 //页面中使用
 <div id="app">
    <my-hand></my-hand>
 </div>
```
# 初始化vue时传入对象中的属性
## **`el`**：
表示vue的属性和方法对哪个DOM根元素起作用（对它的后代元素也起作用）
## **`data`** ：
data中的内容表示需要双向绑定时用到的数据，写在data中的属性都会挂载到当前Vue的实例上；（可以直接用vm.msg来调用）；
## **`methods`** ：
methods中的方法也将挂载到Vue的实例上。可以直接通过vm实例访问这些方法，或者在指令表达式中使用。方法中的 this 自动绑定为 Vue的实例。
在**`指令表达式中`**使用方法的时候，如果方法名后面不写括号，则会默认传入事件对象MouseEvent。写括号代表要传递参数，而不是直接执行，要手动传入事件对象$event。
	**<font color=red>注意</font>**：
- 1、methods中的方法名不能和data中的变量名一样，因为都会挂载到vue的实例上，重名会报错
- 2、不应该使用箭头函数来定义method中的方法，因为使用箭头函数会改变函数中的this，而我们要保证方法中的this都是vue的实例
- 3、{ {} }中执行method中的方法，方法后面必须要加（），才代表执行

## **`filters`**：
允许你在filters中定义自己的过滤器，可被用于一些常见的文本格式化。过滤器可以用在两个地方：双花括号中和 v-bind 表达式 (后者从 2.1.0+ 开始支持)。过滤器应该被添加在 JavaScript 表达式的尾部，由“管道”符号“｜”来指示；
**<font color=red>注意</font>**:
1、filters方法中的this都是window
2、**`｜`**后面的方法执行的时候，会默认的把**`｜`**之前的值当作第一个参数传递给**`｜`**后面的方法 ;
3、filters中的方法在{ {} }中执行的时候，可以不写括号；
```javascript
	<!-- 在双花括号中 -->
	{{ message | toFixed(2)}}//toFixed方法执行时不改变原数据，只是改变message的显示效果
	<!-- 在 `v-bind` 中 -->
	<div v-bind:id="rawId | toFixed"></div>
	filters: {
	   toFixed(val,pra){
	       return '￥'+val.toFixed(pra);
	   }
	}
```
## **`computed`**
计算属性（会有缓存），不是方法：某一个属性的值依赖于其他值的改变而改变时，可使用computed，把需要绑定的属性写在computed中，只会跟依赖的值产生关系。属性中默认会有set和get方法，如果把计算属性写成函数，则默认只调用get方法（get方法必须要有返回值）；computed不支持异步；
## **`watch`**:
监听属性（默认只监控所给属性的第一层）：watch中的方法名应该与data中的要监听的属性名相同。只有当`监听的值发生变化`的时候才会触发对应的方法；watch支持异步，当需要异步的时候，watch还支持设置中间状态；如果想要深度监控，则需要写为对象的形式，并且修改`deep：true`来实现深度监控；
## **<font color=red>计算属性和方法的区别</font>（computed与method的区别）**：
1、缓存：计算属性会根据依赖的属性（归vue管理的数据，可以响应式的变化）进行缓存，只有在它的相关依赖发生改变时才会重新求值；
2、在{ {} }中使用的时候，方法名后必须要加（）来执行，而计算属性如果不需要传递参数，则不需要写（）；
3、方法是不管数据有没有发生改变，都会重新计算，且get方法必须有返回值；
## **<font color=red>什么时候用computed什么时候用watch</font>**：
当数据依赖于多个值的改变而改变时，我们就需要用computed；
```javascript
	<div id="app">
	    <input type="text" v-model="val"> {{fullName}}
	</div>
	let vm=new Vue({
	        el:'#app',
	        data:{
	            val:'',
	            firstName:'zfpx',
	            lastName:'zy'
	        },
	        computed:{
	            fullName(){
	            //只要依赖的其中一个值改变，就会执行此方法；
	                return this.firstName+this.val+this.lastName;
	            }
	        }
	    });
```
当监听的值发生改变的时候，数据需要跟着变，就要用watch，需要异步操作的时候，就必须用watch；
```javascript
let vm=new Vue({
        el:'#app',
        data:{
            val:'',
            firstName:'zfpx',
            lastName:'zy',
            fullName:''
        },
        watch:{
  //val的值改变了才会触发下面的方法,而且会默认传入两个参数，新的值和老的值
            val(newV,oldV){
                this.fullName=this.firstName+this.val+this.lastName;
            }
       //如果写成一个函数的形式（如上的形式），则只会监控第一层级属性对应的值，第一层级的值发生改变才会触发函数执行；如果要实现深度监控，则要写成如下对象的形式,将deep深度监控属性赋值为true；
	       val:{
	       //监控时发生改变就会触发此方法，方法名必须是handler
		      handler(newV){
	             localStorage.setItem('todo',JSON.stringify(newV));
	          },
	          deep:true
	        }
        }
    });
```
{ {} }：其中可放表达式、可以放赋值运算、计算、三元运算符（尽量少写逻辑运算）
# vue的实例拥有的属性
此处的this是vue的实例
`this.$data` vm上的响应式的数据，是个对象
`this.$watch` 监控
`this.$el` 挂载的DOM根元素
`this.$set` 后加的属性实现响应式的变化
`this.$nextTick(()=>{})`  异步方法，渲染DOM完成后获取到需要的最新的数据
`this.$refs.xxx` 获取ref值为xxx的vue对象（可通过`this.$refs.xxx.$el`获取DOM元素）（通过v-for循环出来的可以获取多个，否则只能获取一个）
```javascript
	尽管有 prop 和事件，但是有时仍然需要在 JavaScript 中直接访问子组件。为此可以使用 ref 为子组件指定一个引用的值。如：
	<div id="parent">
		 <user-profile ref="profile"></user-profile>
	</div>
```
<font color=red>**注意：DOM渲染是异步的，如果数据变化后想要获取最新的真实dom中的内容，需要等待页面渲染完成后再去获取，所有的DOM操作最好放在nextTick方法中异步来获取最新的DOM；**</font>
## 为什么data中的属性会出现响应式的变化?
vue会循环data中的属性，依次增加getter和setter方法，来实现响应式的变化。（只要是data中存在的属性都会被增加getter和setter方法，不存在的属性不会实现双向数据绑定。下面的例子中属性a中没有school属性，所以修改vm.a.school的属性值不会导致视图刷新。）
使用`实例.$set`可以给对象添加响应式的数据变化例如：`vm.$set(vm.a,'school',1)`
```javascript
let vm=new Vue({
        el:'#app-3',
        data:{
            msg:'zhufeng'，
            a:{}
        }
        methods:{
		}
  });
  //页面中使用
 <div id="app">
    {{a.school}}
 </div>
```
# Vue中的Class样式与style样式动态绑定（动态的优先级高于原来的）
<font color=red>**：class="{}"或者：class="[]**</font>：
通过：class="{}"或者：class="[]来动态的绑定class样式，与原来的class样式不冲突。
有两种方式：
1、对象的方式绑定
2、数组的方式绑定
```javascript
	<div id="app">
<!--以对象的方式动态绑定class样式：当flag为true时，z样式生效。y样式不生效-->
	    <div class="x" :class="{z:flag,y:false}">我的世界</div>
<!--以数组的方式动态绑定class样式：y，z两个样式都会生效-->
       <div class="x" :class="[y,z]">我的世界</div>
	</div>
	let vm=new Vue({
        el:'#app',
        data:{
            flag:true
        }
    });
```
<font color=red>**：style="{}"或者：style="[]"**</font>：绑定行内的样式
通过上述的方式来动态绑定行内的样式
```javascript
 <div id="app">
 //第一种是对象的方式
	 <div :style="{backgroundColor:'red',color:'pink'}">我是谁</div>
//第二种是数组的方式
	 <div :style="[str1,str2]">我的世界</div>
 </div>
	let vm=new Vue({
        el:'#app',
        data:{
	        str1:{backgroundColor:'blue'},
            str2:{color:'yellow'}
        }
    });
```
# 根据hash实现单页面开发
通过hash记录跳转的路径（可以产生历史管理）
浏览器自带的历史管理方法history（history.pushState(),push到哪里就跳转到哪里），可能会导致404错误（因为用户可能会随意输入路径）
开发时使用hash的方式，上线的时候使用history的方式
[使用hash存储]
```javascript
//存储：存储的时候要把存储的对象转换成字符串
 localStorage.setItem('todo',JSON.stringify(obj));
//获取：获取的时候要把字符串转换成对象
JSON.parse(localStorage.getItem('todo'));
```



