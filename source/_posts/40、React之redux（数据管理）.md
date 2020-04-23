---
title: 40、React之redux(数据管理)
categories: React
toc: true
---

随着javascript单页应用开发日趋复杂，管理不断变化的state十分困难。Redux的出现就是为了解决state的数据管理问题；
## 数据传递
在React中，数据在组件中是单向流动的。在日常开发中经常会遇到非父子组件之间数据传递的场景，可通过以下方法解决：
1、找到共同的父级组件，给父级组件设置状态，然后通过属性向后代组件传递，进行数据交互
2、把数据通过localstorage存储到本地，哪个组件用到的时候再从本地获取，这时通信就变得比较麻烦；
## Redux思想以及常用方法
Redux是将整个应用的状态存储到一个地方，称为store，里边保存着一个状态树(state可以存储多个状态)，组件只能通过派发(dispatch)行为(actions)给store中的管理者(reducer),让管理者来修改state。组件内部通过订阅store中的状态(state)来刷新自己的视图；
redux中常用方法
- createStore：用来创建存放状态的方法，提供有关操作状态树的方法
- applyMiddleware：redux中给我们提供的用来应用中间件的组件
- combineReducers：用于将多个reducer合并成为一个reducer
- bindActionCreators：如果connect第一次执行传递的第二个参数是一个对象，那么就会把它包装成一个函数；

## react-redux中常用的方法
- connect：用于将 UI 组件包装成容器组件。
- Provider：可以让容器组件拿到state。

## Redux概念解析
### store
store就是存放状态树的容器，整个页面只能有一个store
Redux提供了createStore方法来生成store
```javascript
import {createStore} from 'redux';
const store = createStore(reducer);
```
### state
存放在容器中的状态，可以用store.getState()来获取最新的状态，只能让reducer来修改
### action
是一个对象，表示要把状态修改为什么，必须要有一个type属性，对应action的类型，其他属性可以自由设置
### action Creator
由于有时需要多次修改state的同一个类型，如果都手写，会很麻烦。我们可以写成一个函数的形式来生成action，这个函数就叫做action Creator
```javascript
//下面这种写法很麻烦
store.dispatch({type:Types.INCREMENT,count:10})
store.dispatch({type:Types.INCREMENT,count:100})
//使用下面这种写法很方便
function add(count) {
  return {type:Types.INCREMENT,count:count}//名字和值相同可以只写一个
}
function minus(count) {
  return {type:Types.DECREMENT,count}
}
store.dispatch(add(10))
store.dispatch(add(20))
```
### dispatch(action)
用来派发修改状态的任务，参数为要修改的行为(action),是一个对象，存放要修改的类型，和要修改的内容
```javascript
store.dispatch({type:Types.INCREMENT,10})
```
### reducer(state,action)
创建容器的时候传入的管理状态的管理员，一个容器只能有一个管理员
管理员的作用有两个
- 1、在刚创建容器的时候传入管理员，可以将容器中的状态(容器中的状态默认是undefined)初始化为reducer传入的默认状态
```javascript
function reducer(state={number:0},action) {
  switch (action.type){
    case Add:
      return {number:state.number+action.count};
    case Minus:
      return {number:state.number-action.count};
  }
  return state;//切记：返回默认状态
}
let store=createStore(reducer);//此时store中的state的值就变为了{number:0}
```
- 2、修改状态(非常重要，只有reducer可以修改容器中的状态)
拿到容器中当前的state，根据dispatch派发的action，做对应的处理，返回一个新的状态赋值给容器中的state；

### getState()
store.getState():获取容器中当前的状态；
### subscribe(fn)
store.subscribe(fn):订阅状态，当状态改变的时候要执行哪些方法。参数为状态改变的时候需要执行的方法；

### 模拟redux中的createStore方法
```javascript
function createStore(reducer) {
  let state;
  let getState = () => {
    return JSON.parse(JSON.stringify(state));//不修改原来的对象，返回一个新的对象
  };
  let listeners = [];//存放订阅了状态的方法
  //派发任务的方法(参数为要修改状态的行为)
  let dispatch = (action) => {
    //reducer:根据仓库中老的状态,和dispatch派发的action，返回一个新的状态覆盖掉仓库中的state
    state = reducer(state, action);
    //状态改变之后重新执行订阅的事件
    listeners.forEach(item => item());//状态发生了改变之后让订阅了状态的方法执行
  };
  //创建此容器的时候需要根据reducer传入的状态初始化一下仓库中的状态：因为只是为了拿到reducer的默认状态，不需要修改，所以action为空；
  dispatch({});
  //订阅状态：当状态改变的时候要执行哪些事情
  let subscribe = (fn) => {//把订阅了状态的方法放入listeners中
    listeners.push(fn);
    return () => {
      listeners.filter(item => item !== fn);
    }
  };
  return {getState, subscribe, dispatch}//返回三个方法
}
```
## combineReducers:将多个reducer合并为一个
由于一个容器中只能有一个reducer，所以当状态树（state）中有多个属性需要管理的时候就需要把多个reducer合并成一个reducer，当合并后的reducer执行的时候，还是分别到每一个reducer中进行处理，合并的reducer只是将多个reducer处理之后的结果统一起来放在一个对象当中,然后返回
```javascript
let combineReducers= (reducers) => {//把多个管理者以对象的方式传入到方法中 如{counter:counter,todo:todo}
  return (state = {}, action) => {//返回一个reducer
    let obj = {};//最终的状态
    for (let key in reducers) {
      obj[key] = reducers[key](state[key], action);
    }
    return obj;
  }
};
```
## 获取到store中的状态后如何让视图刷新
有两种方法：
- 方法1：由于影响视图刷新的只有state和props，所以我们拿到store中的状态后还要绑定给组件中的this.state,然后订阅一下，当store中的状态更新了，订阅的方法会执行，在订阅的方法中我们调用this.setState来重新设置一下组件的状态，这样视图就更新了；（如果这样写，每个需要状态的组件都要写一遍，很麻烦）
```javascript
export default class Counter extends React.Component {
  constructor(){
    super();
    this.state={n:store.getState().counter.num}
  }
  componentDidMount(){
    this.un=store.subscribe(()=>{
      this.setState({n:store.getState().counter.num});
    })
  }
  render(){
    return (
      <div>
        <button onClick={()=>{
          store.dispatch(actions.add(10))
        }}
        >+</button>
        {this.props.num}
        <button onClick={()=>{
          store.dispatch(actions.minus(10))
        }}>-</button>
      </div>
    )
  }
}
```
- 方法2：使用`react-redux`中提供的`connect`函数
```javascript
import React from 'react';
//* 代表所有，把../store/action/counter里所有的导出结果作为一个actions对象
import * as actions from '../store/action/counter'
import {connect} from 'react-redux';
//导出的是connect链接后的组件
class Counter extends React.Component {
  render(){
    return (
      <div>
        <button onClick={()=>{
          this.props.add(10)
          // store.dispatch(actions.add(10))
        }}
        >+</button>
        {this.props.num}
        <button onClick={()=>{
          this.props.minus(10)
          // store.dispatch(actions.minus(10))
        }}>-</button>
      </div>
    )
  }
}
//mapDispatchToProps可以传入一个actionCreateor对象{会直接把actions对象的属性一个个的挂载到实例的this.props上}
//connect:会把前面传入的两个函数执行的返回结果中的属性放在后面传入的组件的实例上可以用this.props获取
export default connect((state)=>({...state.counter}),actions)(Counter);//第二次执行的参数是当前组件
```
### connect的实现原理
connect需要执行两次
connect第一次执行的时候需要传递两个函数（第二个参数也可以传递一个对象）
connect第二次执行需要传入一个组件，第二次执行时会把第一次connect执行传入的两个函数执行
- 第一个函数执行时会默认传入容器中的状态,第二个函数执行时会默认传入dispatch方法；

然后把两个函数执行的返回结果（一般都是对象）中的属性挂载到传入组件实例的props属性上
```javascript
	let mapStateToProps=(state)=>{//函数的参数是state
	  return {n1:state.counter.num}
	};
	let mapDispatchToProps=(dispatch)=>{//函数的参数是dispatch
	  return {
	    add:(count)=>{
	      dispatch(actions.add(count))
	    },
	    minus:(count)=>{
	      dispatch(actions.minus(count));
	    }
	  }
	};
    export default connect(mapStateToProps,mapDispatchToProps)(Counter)

//利用了Provider的所有后代组件都可以拿到父组件属性的方式
let connect = (mapStateToProps, mapDispatchToProps) => (Component) => {
//如果第二个参数传递一个actions(一个对象)；做如下处理（与bindActionCreators处理方式相同）
  if({}.toString.call(mapDispatchToProps).slice(8,-1)==='Object'){
    let actions=mapDispatchToProps;
    mapDispatchToProps=(dispatch)=>{
      let obj={};
      for (let key in actions) {
        if (actions.hasOwnProperty(key)) {
          obj[key]=(...args)=>{
            dispatch(actions[key](...args));
          }
        }
      }
      return obj;
    }
  }
  return class Prox extends React.Component {
    constructor(props, context) {//constructor:第二个参数为父组件定义的上下文
      super();
      this.state = mapStateToProps(context.store.getState());
    }
    //获取到从父组件返回的上下文
    static contextTypes = {
      store: PropTypes.object
    };
    //每次状态更新重新执行下面的方法获取到最新的状态
    componentDidMount() {
      this.context.store.subscribe(() => {
        this.setState(mapStateToProps(this.context.store.getState()));
      });
    }
    render() {
      //渲染传入的组件并把容器中的状态和dispatch挂载到组件实例的props属性上
      return <Component {...this.state}
                        {...mapDispatchToProps(this.context.store.dispatch)}
      />
    }
  }
};
```
当connect第一次执行时，第二个参数也可以传递一个对象；
如果传递了一个对象，则在connect方法内部会调用`bindActionCreators`方法对actions进行处理，然后把处理后的结果再当成第二个函数执行。而且会自动的帮我们调用dispacth方法，所以我们在写actions的时候就不需要再自己手动调用dispatch方法了
```javascript
export default connect((state=>({...state.counter})),bindActionCreators(actions))(Counter)

// 这个方法是redux中的
let bindActionCreators = (actions) =>{ //为什么可以直接传入一个actions,在内部会用这个函数进行包装
  return (dispatch)=>{
    let obj = {};
    for(let key in actions){
      obj[key] = (...args)=>{
        dispatch(actions[key](...args))
      }
    }
    return obj;
  }
};
```
## 智能组件与木偶组件
智能组件：可以从redux中获取数据，而且可以向redux派发任务修改state
木偶组件：不能直接从redux中获取数据，只能拿到从智能组件中传递过来的数据进行渲染，数据来源于props；也叫作UI组件；
## 高阶组件
为什么需要高阶组件？
当每个组件都需要处理一段公共逻辑时，如果每个组件都写一遍处理逻辑，会很麻烦，此时就需要用到高阶组件；
高阶组件也就是将组件在原有的基础上进行包装。
如获取本地存储的高阶组件
```javascript
import React from 'react';
let local=(key)=>(Component)=>{
    return class LocalHight extends React.Component{
        constructor(){
        super();
        this.state={[key]:''}//此处写[key]是获取的key所代表变量的值
      }
      componentWillMount(){
        let val=localStorage.getItem(key);
        this.setState({[key]:val});
      }
      render(){
        //渲染传入的组件，把LocalHight组件上的状态以props的形式传递给要渲染的Component组件
        return <Component {...this.state}/>;
      }
    }
};
export default local
```
## 修饰器
一个函数就是一个修饰器，也可以称为修饰函数，可以修饰用声明类方式生成的组件，不能修饰用函数方式生成的组件（用函数生成的组件存在变量提升）
需要安装"transform-decorators-legacy" 包之后才可使用
用法：
将修饰函数写在需要修饰的类的上方，修饰函数前面需要加**`@`**,修饰函数执行的时候会把修饰的类当成参数传入到修饰函数中；
```javascript
import {connect} from 'react-redux';
import actions from "../../store/actions/session";
@connect(state=>({...state.session}),actions) //此处不需要再写第二次执行了，会默认的让connect修饰函数执行并把MHeader组件传入；修饰函数必须和要修饰的组件连在一起；
export default class MHeader extends React.Component {//此处导出的是修饰后的组件；
  render(){
    return (
      <div>
      </div>
    )
  }
}
```
## context：上下文（可实现跨组件交互）
每个组件的实例都有context属性，默认值为{}；
父组件可以通过拿到后代组件的上下文，并通过上下文传递给后代组件数据;(好处在于可以跨组件传输， 即爷爷可以传递给孙子)
要用到的两个属性和一个方法
- static 属性：
 - childContextTypes：父组件用来定义传递给后代组件数据的类型
 - contextTypes ：后代组件用来定义接受数据的类型（想要接受父组件传递的哪个数据，就定义同样的类型即可）
- getChildContext：父组件用来获取后代组件上下文的方法，返回值为后代组件的上下文;（可在返回值当中传递后代组件需要的数据）;
设置了之后，后代组件就可以通过this.context.xxx来获取父组件中的数据了；
```javascript
import React from 'react';
import PropTypes from 'prop-types'
import Header from "./Header";
//爷爷组件
export default class App extends React.Component {
  constructor(){
    super();
    this.state = {color:'red'}
  }
  static childContextTypes = { //定义传递给后代组件数据的类型
    color:PropTypes.string,
    setColor:PropTypes.func
  };
  setColor = (color) =>{
      this.setState({
        color
      })
  };
  getChildContext(){
  //返回值为后代组件的上下文
    return {color:this.state.color,setColor:this.setColor}
  }
  render(){
    return <div>
      <Header/>
    </div>
  }
}

//儿子组件
export default class Header extends React.Component {
  static contextTypes = {
     setColor:PropTypes.func
  };
  render(){
    return <div>
      <button onClick={()=>{
        this.context.setColor('green');
      }}>变绿</button>
      <Title/>
    </div>
  }
}
//孙子组件
export default class Title extends React.Component {
  static contextTypes = {//定义要从父组件获取的数据的类型
    color:PropTypes.string
  };
  render(){ // 通过context获取父组件定义的数据
    return <div style={{color:this.context.color}}>Title</div>
  }
}
```
## Provider组件
react-redux 提供Provider组件，可以让Provider中的根组件以及根组件中的所有组件都能拿到容器（store），从而拿到容器中的的state。（Provider中只能有一个根组件）；
```javascript
import React,{Component} from 'react';
import ReactDom,{render} from 'react-dom';
import Counter from './components/Counter';
import Todo from "./components/Todo";
import store from './store';
import {Provider} from 'react-redux';
render(<Provider store={store}>
  <div>
    <Counter/>
    <Todo/>
  </div>
</Provider>,window.root);
```
上面代码中，Provider在根组件（`<div>`）外面包了一层,这样一来，`div`的所有后代组件就默认都可以拿到store了。 它的原理是利用React组件的context属性;
Provider实现原理如下
```javascript
import React from 'react';
import PropTypes from 'prop-types'
export default class Provider extends React.Component {
  //定义传递给后代组件数据的类型
  static childContextTypes = {
    store: PropTypes.object
  };
  getChildContext() {
  //返回值为后代组件的上下文
    return {store: this.props.store};
  }
  render() {
    return (
      //渲染的时候渲染的是Provider下的子节点
      this.props.children
    )
  }
}
```
## 中间件的应用
applyMiddleware组件：redux中给我们提供的用来应用中间件的组件：
如何使用：
1、从redux中导入applyMiddleware组件，
2、安装需要用的中间件模块
3、在createStore的第二个参数中应用中间件
```javascript
import {createStore,applyMiddleware} from 'redux';
//applyMiddleware:redux中的方法，可以帮我们应用中间件
import reducer from './reducer';
import reduxLogger from 'redux-logger';//在控制台显示每次操作的日志
import reduxThunk from 'redux-thunk';//可以让我们派发任务的方法返回一个函数
import reduxPromise from 'redux-promise';//可以在actionCreator中返回一个promise对象，它会等待成功后将成功的结果派发出去
//第二个参数为应用中间件：在控制台中可以显示每次的操作；
let obj=createStore(reducer,applyMiddleware(reduxLogger,reduxThunk,reduxPromise));
window._store=obj;
export default obj ;
```
常用的中间件
1、**`redux-logger`**：可在控制台显示每次操作状态的日志
2、**`redux-thunk`**：可以让我们actions中的方法返回一个函数（原本是返回一个对象）
```javascript
import * as Types from '../action-types';
import {getSliders} from "../../api/home";
let actions={
//选择课程
  changeLesson(lesson){
    return {type:Types.SELECTLESSON,lesson}
  },
  //获得轮播图的数据
  getSlidersAPI(){
  // redux-thunk可以返回函数 将函数执行并且将dispatch的权限交给你来处理
    return (dispatch,getState)=>{
//redux-promise的用法：可以将payload的promise执行，将执行后返回结果赋值给payload
      dispatch({type:Types.SETSLIDERS,payload:getSliders()})
    }
  }
};
export default actions;
```
3、**`redux-promise`**：可让actions中的方法返回一个promise对象，他会等待成功后将成功的结果派发出去
如果actions中的方法返回的是一个promise对象，是不管失败的，只有成功了之后才会把数据返回
如果actions中的方法返回的是一个对象，对象属性中有payload，而且payload是一个promise对象，它会让这个promise执行，会把成功或者失败的结果都进行派发；写成对象之后,reducer对应的方法中使用的就是action.payLoad属性所对应的值了；
```javascript
import  * as Types from './action-types';
let actions={
  add:(count)=>{
    //没使用reduxThunk之前返回的是一个对象
    // return {type:Types.ADD,count};
    //使用了reduxThunk之后可以返回一个函数，执行add的时候会将函数执行并且将dispatch的权限交给你(可以发送异步操作)
    return function (dispatch, getState) {
      setTimeout(()=>{
        dispatch({type:Types.ADD,count});
        dispatch({type:Types.ADD,count});
        dispatch({type:Types.ADD,count});
      },1000)
    }
  },
  minus:(count)=>{
    // return {type:Types.MINUS,count};
    //使用了reduxPromise之后，可以在执行minus的时候返回一个promise对象，会等待成功后将成功的结果派发出去
    //默认返回promise的写法是不管失败的
    return new Promise((resolve)=>{
      setTimeout(()=>{
        resolve({type:Types.MINUS,count});
      })
    })

    //如果返回一个对象，对象属性中有payload，而且payload是一个promise对象，它会让这个promise执行，会把成功或者失败的结果都进行派发
    //写成对象之后,reducer对应的方法中使用的就是action.payLoad属性所对应的值了
    return {
      type:Types.MINUS,
      payload:new Promise((resolve,reject)=>{
        setTimeout(()=>{
          if(Math.random()>0.5){
            resolve(1000);
          }else{
            reject(2000);
          }
        },500)
      })
    }
  }
};
//此处默认导出的是dispatch时用到的方法：需要在connect执行的时候当作第二的参数传入，connect方法会把actions中的属性一个个的挂载到后面传入组件实例的props上
export default actions;
```
