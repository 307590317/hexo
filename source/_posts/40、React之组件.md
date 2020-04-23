---
title: 40、React之组件
categories: React
toc: true
---
# react中的组件
react元素是是组件组成的基本单位
组件有两种声明方式
## 第一种方式是函数声明
每一个创建的组件都是一个自定义的标签，在标签上设置的属性都会转换为react对象的props属性，当react渲染DOM的时候会把props传递给下面例子中的Build函数
```javascript
import React from 'react';
import ReactDOM,{render} from 'react-dom';
let school1={name:'zfpx',age:10};
let school2={name:'珠峰',age:8};
let Build=function (props) {// "函数"(组件)的参数是属性
    return <p>{props&&props.name}{props&&props.age}</p>;
};
render(<div>
    <Build name={school1.name} age={school1.age}/>
    <Build {...school2}/>//利用解构赋值将对象中的内容解构出来传递给Build组件
    <h1></h1>
</div>,window.root);
```
## 第二种是以类的形式声明
```javascript
import React,{Component} from 'react';
import ReactDOM from 'react-dom';
let school1 = {name:'珠峰',age:8};
let school2 = {name:'珠峰',age:0};
//1.类声明的组件有this this上有props属性
class Build extends Component{//Component这个基类上有this.setState
    render(){ // 这个组件在调用时默认会调用render方法(此render相当于注册组件)
        let {name,age} = this.props;
        return <p>{name} {age}</p>
        // return  <p>{this.props.name} {this.props.age}</p>
    }
}
ReactDOM.render(
此render的渲染步骤
1）先渲染外层div，将jsx元素转化为ES5的语法，然后调用React.createElement方法创建react对象，当遇到div下的子元素的时候，会进行以下操作
//1、发现Build 是一个继承Component的类，它会创建这个类的一个实例，并且执行原型上的render方法
//2、执行原型上的render返回JSX元素，然后继续执行外层的render
2）继续将上面返回的jsx元素转化为es5语法，调用React.createElement方法创建虚拟DOM元素对象，Build 这个位置被返回的元素对象替换
3）外层ReactDOM.render将上面生成的react对象渲染成真实的DOM结构，并追加到页面中；
<div>
    <Build name={school1.name} age={school1.age}/>
    <Build {...school2} />
</div>,window.root);
```
区别：类声明有状态，this，和生命周期
声明组件的规则：
- 1、首字母必须大写,目的是为了和JSX元素进行区分
- 2、组件定义后可以像JSX元素一样进行使用
- 3、可以通过render方法将组件渲染成真实DOM（做了优化 只会重新渲染改变的地方）

单闭合标签不能添加子节点；双闭合标签可以
## 组件中属性(props)和状态(state)的区别
属性和状态的变化都会影响视图更新,但是两者导致视图刷新的机制是不一样的；
- 1、确切的说不是因为props更新了导致的视图刷新，而是传递属性的组件重新的渲染了，从而导致接受属性的组件也重新渲染，这样属性就也随着更新了；
- 2、状态导致的视图刷新：只要通过this.setState改变状态，视图就会刷新；

组件的数据来源有两个地方：
### 通过props(属性) 获取数据：
外界传递进来的（存在默认属性和属性校验）,是只读的，不能修改；
有属性校验模块：prop-types（用来校验属性的类型和是否必须给）
用yarn add prop-types来安装。用来校验属性的
```javascript
import React,{Component} from 'react';
import ReactDom,{render} from 'react-dom';
import PropTypes from 'prop-types';
//   1、默认的属性必须写在defaultProps中（不能改名字）
//   2、校验器的名字必须是propTypes(不能改名字)；
class School extends Component{//类上的属性（把类当作对象添加的属性）就叫做静态属性
    static defaultProps={//会先默认调用defaultProps
        name:'珠峰',
        age:1
    };
    static propTypes={//校验属性的值（number）类型和是否必须给（isRequired）
        age:PropTypes.number.isRequired
    };
    constructor(props){
  //如果想在constructor中使用props，则只能传递参数props，在constructor外面可以直接用this.props
        //不能在组件中更改属性
        super();
	    console.log(this.props)//undefined
    }
    render(){//渲染组件的，返回一个组件
    //如果没有默认的defaultProps，也没有传递props，则this.props是一个空对象
        return <h1>{this.props.name} {this.props.age}</h1>
    }
}
//name={'我是谁'}:这样写大括号中就可以是变量
render(<School name={'我是谁'} age={9}/>,window.root);

```
### this.state(状态) 状态是自己的：
是可读写的（写了状态就必须给默认值）；
我们可以通过this.setState({})方法来修改状态，由于修改状态会引起视图刷新（即更新DOM），这里面要经过React核心中的diff算法，最终才能决定是否进行重渲染，以及如何渲染。而且为了批次与效能的理由，多个setState有可能在执行的过程中需要被合并，所以它被设计成异步来执行时相当合理的。
```javascript
import React,{Component} from 'react';
import ReactDom,{render} from 'react-dom';
//1、state变化可以更新视图,只能通过this.setState({})来更改状态,调用后会更新视图
class Counter extends Component{
    constructor(){
        super();
        console.log(this.state);//默认为undefined
        this.state={count:0}
    }
    add=val=>{
        //setState方法会默认按最后的设置进行执行
        // this.setState({count:this.state.count+val});
        this.setState({count:this.state.count+val});//会执行这个
        //setState有两种写法，一种是传入对象的方式，一种是传入函数的方式
        //下一个状态是依赖于上一个状态时，需要写成函数的方式
        this.setState(prevState=>({count:prevState.count+val}));//如果返回的就是一个对象，则需要用'()'包裹
        }); //等同与下面这种写法
        //this.setState({count:this.state.count+val},function () {
      //      this.setState({count:this.state.count+val});
    //};
    render(){
        return <p>
            {this.state.count}
            <button onClick={this.add.bind(null,2)}>+</button>
        </p>
    }
}
render(<Counter/>,window.root);
```
## 复合组件与数据传递
复合组件：将多个组件进行组合嵌套；
复合组件之间的数据传递：
### **<font color=red>父传子</font>**
父组件传递给子组件数据通过设置属性的方式
```javascript
import React,{Component} from 'react';
import ReactDom,{render} from 'react-dom';
import PropTypes from 'prop-types';
import 'bootstrap/dist/css/bootstrap.css';
//1、什么是复合组件，将多个组件进行组合
//2、结构非常复杂时可以把组件分离开
//3、复合组件 有父子关系，父的数据传递给子(通过给子组件设置属性)
// Panel组件 Heading Body
class Counter extends Component{
    render(){
    //由于在Counter中传递了属性，所以这里可以用this.props获取到；
        let {header,body}=this.props;
        return (
            <div className={'container'}>
                <div className='panel-default panel'>
                //这里写了之后就可以在下面的子组件中拿到对应的props值
                    <Header header={header}></Header>
                    <Body body={body}></Body>
                </div>
            </div>
        )
    }
}
//react中需要把属性一层一层向下传递：单向数据流；
class Header extends Component{
    render(){
        return (
            <div className='panel-heading'>
            //上面传递了之后就可以直接在此处使用this.props属性了；
                {this.props.header};
            </div>
        )
    }
}
class Body extends Component{
    render(){
        return (
            <div className={'panel-body'}>
                {this.props.body}
            </div>
        )
    }
}
let data={header:'我很帅',body:'长的英俊'};
render(<Counter {...data}/>,window.root);
```
### **<font color=red>子传父</font>**
通过父亲传递给儿子一个函数，儿子调用父亲的函数将要改的值传递给父亲，父亲更新值，刷新视图；
```javascript
import React, {Component} from 'react';
import ReactDom, {render} from 'react-dom';
import 'bootstrap/dist/css/bootstrap.css';
//1、子传父 通过父亲传递给儿子一个函数，儿子调用父亲的函数将要修改的值传递给父亲，父亲更新值，刷新视图；
class Counter extends Component {
  constructor() {
    super();
    this.state = {color: 'primary'};
  }
  changeColor = (color) => {
    this.setState({color});
  };
  render() {
    let {header} = this.props;
    return (
      <div className='container'>
        <div className={'panel panel-' + this.state.color}>
          <Header header={header} color={this.changeColor}></Header>
        </div>
      </div>
    )
  }
}
//react中需要把属性一层一层向下传递：单向数据流；
class Header extends Component {
  clickColor = () => {
    this.props.color('danger');//儿子调用父亲的方法，把要改的值传递给父亲，让父亲自己修改
  };
  render() {
    return (
      <div className='panel-heading'>
        {this.props.header}
        <button className={`btn btn-danger`} onClick={this.clickColor}>改颜色</button>
      </div>
    )
  }
}
let data = {header: '我很帅'};
render(<Counter {...data}/>, window.root);
```
## ref属性
ref可以写在组件上也可以写在DOM元素上，获取的时候得到两者的结果是不同的；
- 写在组件上时（这里的组件是有状态的组件），通过refs获取的是组件的实例；
- 写在DOM元素上时，通过refs获取的是具体的DOM元素节点

ref的值可写为两种形式
### 直接写为一个值
通过ref设置的属性：可以通过this.refs.xxx获取到对应ref值为xxx的dom元素或者组件的实例
### 写成一个函数的形式
写成函数时，这个函数将会在渲染成真实DOM结构或组件被挂载后执行，函数的参数为真实的DOM结构或组件的实例
```javascript
//如上面非受控组件中的例子
//1、直接将ref写为一个值
<input type="text" ref='a'/>
//2、将ref写为一个函数
 <input type="text" ref={(x)=>this.c=x}/>
 {/* x 代表真实的DOM(写成函数的形式会默认传入当前设置ref的DOM结构 )，上面这种写法相当于把DOM结构挂载到了this的自定义属性c上，以后可以直接用this.c拿到这个input的DOM结构*/}
resultChange=()=>{//通过ref设置的属性：可以通过this.refs.xxx获取到对应ref值为xxx的dom元素
	this.c.value=parseFloat(this.refs.a.value)+parseFloat(this.refs.b.value);
};
```
## 受控组件与非受控组件
当引入HTML表单元素时，根据是否将数据托管到React组件中还是将其依然保留在DOM元素中来区分为受控与非受控组件，受控组件可以赋予默认的值
### 受控组件
指交由React控制并且所有的表单数据统一存放的组件，需要与onInput/onChange/disabed/readOnly等控制value/checked的属性或事件一起使用
```javascript
import React, {Component} from 'react';
import ReactDom, {render} from 'react-dom';
class Input extends Component {
  //1、受状态控制的组件，必须要有onChange方法，否则不能使用；
  //2、受控组件可以赋予默认值(官方推荐使用受控组件)
  constructor() {
    super();
    this.state = {val: '', a: 1, b: 2};//初始化状态：设置默认值
  }
  inputChange = (val, e) => {//处理多个输入框的值映射到this.state的方法
    //val:要修改的state中的属性的名称
    //e:事件源（e.target.value是要修改成的值）
    this.setState({[val]: parseFloat(e.target.value)||0});//更改状态
  };
  render() {
    return (
      <div>
      //下面两个input都受this.state影响（受控组件）
        <input type="text" value={this.state.a} onChange={this.inputChange.bind(null, 'a')}/>
        <input type="text" value={this.state.b} onChange={e =>{
          //方法执行的时候要用到事件对象e，所以必须要传
          this.inputChange('b', e)
        }}/>
        {this.state.a + this.state.b}
      </div>
    )
  }
}
render(<Input/>, window.root);
```
### 非受控组件（基于ref来管理）
而非受控组件则是由DOM存放表单数据；我们可以使用refs来操控DOM元素
```javascript
import React,{Component} from 'react';
import ReactDom,{render} from 'react-dom';
class Input extends Component{
    //输入框value值不受状态控制，不能初始化默认值
    resultChange=()=>{//通过ref设置的属性：可以通过this.refs.xxx获取到对应ref值为xxx的dom元素
      this.c.value=parseFloat(this.refs.a.value)+parseFloat(this.refs.b.value);
    };
    render(){
        return (
            <div onChange={this.resultChange}>
                <input type="text" ref='a'/>
                <input type="text" ref='b'/>
                {/*x代表真实的DOM(写成函数的形式会默认传入当前设置ref的DOM结构)，下面这种写法相当于把元素挂载到了this的自定义属性c上*/}
                <input type="text" ref={(x)=>this.c=x}/>
            </div>
        )
    }
}
render(<Input/>,document.getElementById('root'));
```
## 受控组件与非受控组件的区别
虽然非受控组件看上去更好实现，可以直接从DOM中获取数据，而不需要额外的代码。不过实际开发中我们不建议使用非受控组件，因为更多情况下我们需要考虑到表单验证、选择性的开启或者关闭点击按钮、强制输入格式等功能的支持，而此时我们将数据托管到React中有助于我们更好的以声明式的方式完成这些功能；引入React的原因就是为了将我们从繁重的直接操作DOM中释放出来
