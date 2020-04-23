---
title: 40、React
categories: React
toc: true
---

什么是React？
- React 是一个用于构建用户界面的框架（采用的是MVC模式）：集中处理VIEW这一层
- 核心专注于视图,目的实现组件化开发

什么是组件化开发？
我们可以很直观的将一个复杂的页面分割成若干个独立组件,每个组件包含自己的逻辑和样式 再将这些独立组件组合完成一个复杂的页面。 这样既减少了逻辑复杂度，又实现了代码的重用
- 可组合
- 可重用
- 可维护

## 安装react脚手架（跑环境）
在全局下安装create-react-app
打开命令行工具输入以下代码
`npm install create-react-app -g`
默认会自动安装React，react-dom两部分
## 使用react脚手架创建项目
需要在哪个文件夹下创建react项目就在那个项目下打开命令行工具输入以下代码
`create-react-app xxx`(xxx为项目名称：不能出现大写字母)
cd xxx(转到项目目录下)
yarn start或npm start启动项目即可
## react和react-dom模块
在js文件的顶部导入react和react-dom
**`react`**：核心模块（生命周期等钩子函数都是在这里定义的）
react 中常用的方法：
- Children：我们可以用React.Children.map去遍历this.props.children获取到的结果
- Component：继承需要用到的
- PureComponent：纯函数
- createElement：在shouldComponentUpdate()中实现时，使用了 props 和 state 的浅比较。

**`react-dom`**：提供与DOM相关的功能
react-dom中常用的方法：
- render ：是 React 的最基本方法，用于将模板转为 HTML 语言，并插入指定的 DOM 节点
```javascript
import React from 'react';//核心基础 模块(生命周期)
//用来把react元素（jsx元素）渲染成真实的DOM结构并添加到页面当中
import ReactDOM,{render} from 'react-dom';
//ReactDOM中就一个方法比较常用叫render
//可以把render从react-dom中解构出来
```
## ES6中的模块导入和导出
```javascript
//=>A模块
import Temp,{lib} from "./B";//=>把导入的Temp中的部分属性方法进行解构赋值
new Temp().init();
lib();//=>Temp.lib()

//=>B模块
export default class Temp {
    init() {
        console.log(`hello world`);
    }
    static lib(){//=>Temp.lib
	    
    }
}
```
## react中的jsx语法与render渲染方法
### JSX语法
react构建页面有自己的语法：JSX语法；
jsx是一种JS和html混合的语法，HTML 语言直接写在 JavaScript 语言之中，不加任何引号，这就是 JSX 的语法，它允许 HTML 与 JavaScript 的混写；JSX最后会通过babel进行转义
jsx中的html部分和原生html基本一样（也有不一样的比如class要用className，label的for要用htmlFor），并且属性名要使用驼峰命名法；
用jsx语法写的内容叫做jsx元素(虚拟DOM元素)；
jsx元素具有不变性 ：只在页面初次加载的时候渲染一次，以后只能通过更改状态和属性来重新渲染；

**`jsx表达式的用法`**
- 1、可以放JS的执行结果：在react中想将js当作变量引入到jsx中需要使用{}
- 2.在jsx中,相邻的两个jsx元素 渲染时需要外面包裹一层元素（只能有一个根元素）
```javascript
	function build(str) {
	    return <h1>{str.name}</h1><h1>{str.name}</h1>;//在jsx语法中，这种写法是错误的
	}
```
- 3、如果多个元素需要在return后面换行，我们需要加一个'()'当作一个整体返回
- 4、可以把JSX元素当作函数的返回值
- 5、用`<`和`{}`来区分是html还是js表达式：遇到 HTML 标签（以` <` 开头），就用 HTML 规则解析；遇到代码块（以` { `开头），就用 JavaScript 规则解析
- 6、循环绑定需要给元素设置唯一的KEY属性（属性值是唯一的），目的是为了方便React做DOMdiff；

**`jsx属性的用法`**
在jsx中分为普通属性和特殊属性
- 1、普通属性：和html中的一样
- 2、特殊的属性如class，要使用className；for（label中的for）要使用htmlFor
- 3、style要采用对象的方式
```javascript
import React from 'react';
import ReactDOM,{render} from 'react-dom';
let str='<h1>纯标签</h1>';
let style={background:'red'};
render(<div>
    <li className="aa"></li>
    <li htmlFor="aa" style={style}></li>
    <li dangerouslySetInnerHTML={{__html:str}}></li>
</div>,window.root);
```
- 4、dangerouslyInnerHTML插入html(基本上用不到)

### **`React.createElement`**：
- React.createElement用来创建一个react对象，具有一个type属性代表当前的节点类型，还有节点的属性props;
```javascript
React.createElement("div",null,"姜,",React.createElement("span",null,"帅哥"))
//结果如下
{type:'div',props:{className:"red",children:['姜',{type:'span',props:{id:'handsome',children:'帅哥'}}]}}
```

### **`render()`**:此方法是由`react-dom`模块中提供的方法
第一个参数为要渲染的jsx元素
第二个参数为要把渲染的DOM结构插入到哪个容器当中
第三个参数为渲染完成后需要执行的回调函数；
- 用来将虚拟DOM元素（jsx元素）渲染成真实的DOM；
```javascript
ReactDOM.render(<div className='app'>姜<span id='handsome'>帅哥</span></div>,window.root);
```
## react中的虚拟DOM
我们在react中创建的组件和用jsx语法写出的标签等，都是虚拟的DOM元素：
组件并不是真实的 DOM 节点，而是存在于内存之中的一种数据结构，叫做虚拟 DOM （virtual DOM）。只有当它插入文档以后，才会变成真实的 DOM 。根据 React 的设计，所有的 DOM 变动，都先在虚拟 DOM 上发生，然后再将实际发生变动的部分，反映在真实 DOM上，这种算法叫做 DOM diff ，它可以极大提高网页的性能表现。
## 虚拟DOM渲染过程（虚拟DOM—>真实DOM）
### 构建虚拟DOM元素（jsx元素）：
- 在js中用jsx语法写的jsx元素就是虚拟DOM元素；(render中的元素)
**`render(<h1 className='red'>我是谁,我在哪</h1>,window.root)`**

### 调用render方法把虚拟DOM元素渲染成真实的DOM结构
- 1）render方法会先用babel把jsx元素转化为ES5语法
**`'h1', {className: 'red'}, '我是谁,我在哪'`**
- 2）再用React.createElement方法根据上面的结果创建出一个react对象
**`React.createElement('h1', {className: 'red'}, '我是谁,我在哪')`**结果如下
**`{type: "h1", props: {className: "red", children: "我是谁,我在哪"}}`**
- 3）此时render方法将react对象转化为真正的DOM结构，渲染到页面上
**`render(<h1 className='red'>我是谁,我在哪</h1>,window.root)`**

自己模拟React.createElement方法与render方法：（render不完善）
```javascript
{type:'div',props:{className:"red",children:['姜',{type:'span',props:{id:'handsome',children:'帅哥'}}]}}
function createElement(type,props,...children) {
  if(children.length === 1) children = children[0];
  return {type,props:{...props,children:children}}
}
let myRender = (obj, container,callback) => {
  let {type, props} = obj;
  let ele = document.createElement(type);//创建第一层
  for (let key in props) {
    if (key === 'children') {
      //children可能是数组也可能是一个字符串（纯文本）
      if ({}.toString.call(props[key]).slice(8,-1)==='Array') {
        props[key].forEach(item => {
          if (typeof item === 'object') {//如果子元素是对象，那就继续调用render
            myRender(item, ele);
          } else {
            ele.appendChild(document.createTextNode(item));
          }
        })
      } else {//字符串的话直接创建文本节点并插入到元素中
        ele.appendChild(document.createTextNode(props[key]));
      }
    } else if (key === 'className') {//className要做单独处理
      ele.setAttribute('class', props[key]);
    } else {
      ele.setAttribute(key, props[key]);//其他的直接设置(style等属性未做处理)
    }
  }
  container.appendChild(ele);//把DOM元素插入到html中
  callback && callback();//=>添加成功后触发回调函数执行
};
```
## react元素中的事件绑定
给react元素（jsx元素）事件绑定对应的方法时，方法如果不做处理，那么执行的时候方法中的this默认是undefined
可通过如下方式解决：
- 1、使用bind方式；（绑定的时候处理）
- 2、使用ES6的箭头函数（绑定时候处理：在外层包一层箭头函数）
- 3、使用ES7的箭头函数(在类的原型上定义的时候处理：写为：函数名=()=>{})

## 获取组件的所有子节点
在函数式声明的组件中我们用props.children来获取组件的所有子节点；
在以类声明的组件中我们用this.props.children来获取组件的所有子节点；
```javascript
render(<Dinner>
  <div>吃啥</div><div>吃啥</div><!--此处相当于组件的子节点->
</Dinner>,window.root);
```
this.props.children的值有三种情况：
- 1、如果组件中间没有子节点，值是undefined；
- 2、如果有一个子节点，获取的是对象数据类型的值
- 3、如果有多个子节点，获取的是数组类型的值
我们可以用**`React.Children.map`**去遍历**`this.props.children`**获取到的结果
```javascript
import React from 'react';
import ReactDom,{render} from 'react-dom';
//this.props.children是获取组件中间的内容
//有三种可能的值：
// 1、不传的话默认是undefined；
// 2、传一个时是对象类型
// 3、传多个是数组类型
//我们可以用React.Children.map去遍历this.props.children
export default class Dinner extends React.Component {
  render(){
    return (
      <div>
        {React.Children.map(this.props.children,(item,index)=>(
          <li>{item}</li>
        ))}
      </div>
    )
  }
}
render(<Dinner>
  <div>吃啥</div><div>吃啥</div>{/*此处相当于组件中间的内容*/}
</Dinner>,window.root);
```
## ES6：export default 和 export 区别
1.export与export default均可用于导出常量、函数、文件、模块等
2.你可以在其它文件或模块中通过import+(常量 | 函数 | 文件 | 模块)名的方式，将其导入，以便能够对其进行使用
3.在一个文件或模块中，export、import可以有多个，export default仅有一个
4.通过export方式导出，在导入时要加{ }，export default则不需要
5、当使用export导出多个的时候，导入时可以使用`import * as xxx form './aaa'`;
```javascript
	a.js 模块
	//存放action的类型
	export const ADD_TODO='ADD_TODO';
	export const CHANGE_SELECTED='CHANGE_SELECTED';
	export const DELETE='DELETE';
	export const CHANEG_TYPE='CHANEG_TYPE';

   b.js 模块要引入a模块导出的内容
	//把从a模块中导出的所有内容作为一个对象赋值给obj
	import * as obj form './a'
	//用的时候可以用obj.ADD_TODO来调用
	obj.ADD_TODO
```