---
title: 40、React之生命周期
categories: React
toc: true
---
# React中的生命周期
在react操作过程中，设定了很多的执行阶段，每个阶段提供了不同方法，
![](http://ww1.sinaimg.cn/large/0067rNTTly1fp94k3m1a3j31280huglz.jpg)

## **[初期渲染]**
### **`defaultProps：`**
- 默认属性设置与校验

### **`constructor：`**
- 获取属性和设置默认初始状态

### **`componentWillMount：`**（只执行一次）
- constructor执行完成后，DOM结构开始加载之前，执行此方法

### **`render`**（开始渲染）

### **`componentDidMount`**（只执行一次）
- DOM结构加载完成后会自动触发此函数

## **[状态发生改变时触发]**
### **`shouldComponentUpdate`**
- 状态更新时会触发此方法，返回布尔值

### **`componentWillUpdate`**
- 如果shouldComponentUpdate返回true则执行此函数，否则不执行

### **`render`**（开始渲染）
### **`componentDidUpdate`**
- 组件完成更新后触发此方法

## **[卸载组件时触发]**
### **`ReactDOM.unmountComponentAtNode`**
- 删除某个组件

### **`componentWillUnmount`**（组件将要卸载）
- 组件移除时会调用此方法,一般在这个方法中我们要清除定时器

## **[属性发生改变时触发]**
### **`componentWillReceiveProps`**
```javascript
import React, {Component, PureComponent} from 'react';
import ReactDom, {render} from 'react-dom';
class Counter extends Component {/*PureComponent是纯组件：比较的是状态的地址，如果是同一个地址，则不会更新，所以状态最好采用新的状态替换掉老的*/
  static defaultProps = {
    name: 'zfpx'
  };
  constructor(props) {
    super();
    this.state = {number: 0};
    console.log('1、constructor');
  }
  componentWillMount() {//同步代码放在此处最好：如获取本地的数据：在渲染之前获取数据，只渲染一次
    console.log('2、父组件将要加载');
  }
  componentDidMount() {
    this.setState({number: this.state.number + 1});
    console.log('5、父组件已经挂载完成');
  }
  click = (e) => {
    this.setState({number: this.state.number + 1});
  };
  //react可以在shouldComponentUpdate方法中优化 PureComponent 可以帮我们做这件事
  shouldComponentUpdate(nextProps, nextState) {//分别代表下一次的属性和下一次的状态
    //组件是否需要更新
    console.log('父组件是否需要更新');
    return nextState.number % 2;//如果此函数返回了false，就不会调用render方法了
  }
  //乱用this.setState({})会造成栈溢出
  componentWillUpdate() {
    console.log('父组件将要更新');
  }
  componentDidUpdate() {
    console.log('父组件完成更新');
  }
  render() {
    console.log(`3、父组件render（渲染）`);
    return (
      <div>
        <p>{this.state.number}</p>
        <ChildCounter n={this.state.number}/>
        <button onClick={this.click}>+</button>
      </div>
    )
  }
}
class ChildCounter extends Component {
  componentWillReceiveProps(newProps) {//第一次不会执行，之后属性更新时才会执行
    console.log('子有新的属性');
  }
  render() {
    console.log('4、child render');
    return (
      <div>
        {this.props.n}
      </div>
    )
  }
  shouldComponentUpdate(nextProps, nextState) {
    console.log('子组件是否需要更新');
    return nextProps.n % 3;
  }
}
render(<Counter name={'计数器'}/>, window.root);
```
# <font color=red>**注意**</font>:
**最好不要在任何一个状态更新时触发的方法中写**`this.setState()`，如果不加条件控制的话，会再次触发状态更新而导致方法执行，这样就会造成栈溢出；
```javascript
/*页面初始化时触发*/
//defaultProps：
//constructor
//componentWillMount(将要挂载)页面初次加载时执行1次
//render
//componentDidMount(完成挂载)页面初次加载完成时执行1次
/*状态更新时会触发*/
//shouldComponentUpdate
//componentWillUpdate
//render
//componentDidUpdate
/*属性更新*/
//componentWillReceiveProps
/*卸载*/
// componentWillUnmount
```