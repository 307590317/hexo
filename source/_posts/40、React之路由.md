---
title: 40、React之路由
categories: React
toc: true
---

# 前端的路由跳转
前端自己处理页面跳转都是两种方式：
1、通过哈希值（带有'#'）：开发时使用hash，不会导致404错误，但是不支持SEO
2、h5提供的historyApi（不带'#'）：上线采用h5的跳转
# react中的路由
1、安装路由模块
**`yarn add react-router-dom`**
2、在组件中导入存放路由的容器
常用的有两个容器：
1、HashRouter。
2、RrowseRouter。
3、MemoryRouter: 在内存中管理history，地址栏不会变化。在reactNative中使用。
上线的时候使用h5
我们开发的时候常用的是HashRouter
# react路由的使用
react路由也提供了对应的两种容器
## 路由容器组件
HashRouter和RrowseRouter是两个路由容器组件，我们要在路由容器中使用Route组件来定义路由；（一个Router容器中只能有一个根元素，所以要在多个路由外面包一层）
## Route组件
Route组件是一条条的路由，在Route中我们使用path定义路径，并使用component指定当路径匹配时需要加载的组件；
**`提示:`Route组件有<font color=red>exact</font>属性,如果值为true，则只有路径完全匹配时才渲染某个组件**
路由的匹配规则：匹配路径的时候会一个路由一个路由的匹配，只要路径匹配上就加载对应的组件；不管第一个是否匹配上了，都会匹配第二个，直到匹配完所有路由；
```javascript
import React from 'react';
//路由的两种方式：
//1、哈希值
//2、h5内置的history
//HashRouter和BrowRouter是路由的容器，是组件，包在路由外面
import {HashRouter as Router,Route} from 'react-router-dom';
function Home() {
  return <h1>首页</h1>
}
function List() {
  return <h1>列表</h1>
}
function Profile() {
  return <h1>个人中心</h1>
}
function User() {
  return <h1>用户</h1>
}
export default class App extends React.Component {
  render(){
    return (
      <Router>
        <div>
{/*当路径匹配的时候,会渲染对应的组件，路径是从上到下匹配的，一个路由一个路由的匹配，只要路径匹配上就渲染对应的组件*/}
{/*如果想要路径完全匹配时才渲染某个组件的话需要添加exact属性为true*/}
          <Route path='/p' exact={true}  component={Home}/>
          {/* 可以匹配到 /list/1   /lista 是匹配不到的*/}
          <Route path='/list' component={List}/>
          <Route path='/profile' component={Profile}/>
          <Route path='/user' component={User}/>
        </div>
      </Router>
          )
  }
}
```
一般情况下，我们在src目录下的index.js中配置路由即可
# Switch组件
由于路由的匹配规则是一个个的进行匹配，而我们想要的是匹配了一个路由之后剩下的就不会再进行匹配，对此，`react-router-dom`中提供给我们`Switch组件`来实现我们想要的功能；
用法：需要把`Switch组件`包在所有`Route`的外层
```javascript
import React,{Component} from 'react';
import ReactDom,{render} from 'react-dom';
import {HashRouter as Router,Route,Switch,Redirect} from 'react-router-dom';
import App from "./containers/App";
import Home from './containers/Home';
import Profile from './containers/Profile';
import User from './containers/User';
import 'bootstrap/dist/css/bootstrap.css';
import Detail from "./containers/Detail";
import ProtectedRoute from "./containers/ProtectedRoute";
//switch的作用是让Route匹配后就停止匹配下面的内容
//exact确切的 表示只有路径完全相等时才匹配
	render(<Router>
	(这样写的话App组件render的时候就要把this.props.children写到返回的标签当中，才能渲染路由)
	  <App>
	    <Switch>
	      <Route path='/' exact={true} component={Home}/>
	      {/*路径匹配是匹配两个'/'中间的内容而不是只匹配一个字母*/}
	      <Route path='/p' component={Detail}/>
	      <Route path='/profile' component={Profile}/>
	      <Route path='/detail/:id' component={Detail}/>
	      {/*如果匹配不到的话会走最后一个(路径不会更改)*/}
	      {/*<Route component={Home}/>*/}(和下面的Redirect只能出现一个)
	      {/*如果匹配不到的话会走最后一个(路径会更改)*/}
	      <Redirect to='/'/>
	    </Switch>
	  </App>
	</Router>,window.root);
```
# Redirect组件
当用户随意输入路径的时候，我们不希望出现404页面，此时我们就需要用到`Redirect组件`，将页面重定向到指定的路径，渲染指定的组件
用法：我们可以写成路由的最后一条，当上面的路由都没匹配到的话，会走最后一个，重定向到我们指定的路径；
Redirect的to属性可以写成两种方式
- 1、字符串（直接写成一个路径）
- 2、写成一个对象  **`to={ {pathname:'/login',from:props.match.url} }`**
form属性用来记录当前匹配的路径，为了以后能再跳转到当前路径；
```javascript
	render(
	<Router>
	 <App>
	  <Switch>
		<Route path='/' exact={true} component={Home}/>
		{/*路径匹配是匹配两个'/'中间的内容而不是只匹配一个字母*/}
		<Route path='/p' component={Detail}/>
		<Route path='/profile' component={Profile}/>
		<Route path='/detail/:id' component={Detail}/>
		{/*如果匹配不到的话会走最后一个(路径不会更改)*/}
		{/*<Route component={Home}/>*/}(和下面的Redirect只能出现一个)
		{/*如果匹配不到的话会走最后一个(路径会更改)*/}
		<Redirect to='/'/>（具有重定向功能而且路径会改变）
	   </Switch>
	 </App>
	</Router>,window.root);
```
# Link组件
相当于vue中的router-link组件，使用Link组件可以替代我们自己写的a标签,因为上线后我们会用到browserHistory，跳转需要用到H5的api进行跳转,Link组件是react路由中提供的声明式组件,可以帮我们区分路由的模式来实现路由的跳转。点击的时候会跳转到对应的路径
用法：Link的to属性可以写成两种方式
- 1、直接写成字符串
- 2、写成一个对象：**`to={ {pathname:'/user',state:要带过去的内容} }`**（不仅能写路径，还能配置一些其他属性，比如state属性，跳转到/user这个页面的时候带过去一些内容，只有通过点击路由导航调到对应页面的时候才能带过去内容）
```javascript
import React from 'react';
import {Link,withRouter} from 'react-router-dom';
class Nav extends React.Component {
  render(){
    return (
      <nav className="navbar navbar-default">
        <div className="container-fluid">
          <div className="navbar-header">
            <a className="navbar-brand" href="#">
              珠峰管理系统
            </a>
          </div>
          <ul className="nav navbar-nav">
            <li><Link to='/'>首页</Link></li>
            <li><Link to={{pathname:'/profile'}}>个人中心</Link></li>
            <li><Link to={{pathname:'/user'}}>用户</Link></li>
          </ul>
          <button className='btn btn-primary' onClick={()=>{
            this.props.history.push('/');
          }}>返回首页</button>
        </div>
      </nav>
    )
  }
}
//withRouter会把传入的组件包装成一个Route组件:
// 包装后组件实例的props属性上就会有history，loaction，match三个属性（通过路由渲染的会自带这三个属性）
export default withRouter(Nav);
```
# NavLink组件
当我们需要给路由导航（Link）添加样式时，我们就要用到NavLink组件；
用法：直接将Link标签替换为NavLink标签，然后在index.html中使用规定的类名**`.active`**添加样式即可；这样当点击某一个路由导航时，对应的样式就会生效；
```javascript
//HTML
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="utf-8">
    <title>React App</title>
    <style>
      .active{
        color: red !important;
      }
    </style>
  </head>
  <body>
    <div id="root"></div>
  </body>
</html>
//组件中的路由导航
<nav className='nav'>
    <li><NavLink to={{pathname:'/user/add'}}>添加</NavLink></li>
    <li><NavLink to={{pathname:'/user/list'}}>列表</NavLink></li>
</nav>
```
# 二级路由
二级路由就是在一级路由渲染的组件中再嵌套路由：由于是在一级路由中嵌套二级路由，所以不需要再写Router，直接写Route即可
**`注意：因为是二级路由，要保证一级路由也同时显示，所以路径开头要和一级路由的路径相同。`**
如果多次点击相同路由时会触发Hash history cannot PUSH the same path;这样的一个警告,这个是无法去除的但是改成BrowserHistory就不会触发此警告了。所以不用担心.
```javascript
	import React from 'react';
	import ReactDOM from 'react-dom';
	import {HashRouter,Route} from 'react-router-dom';
	import 'bootstrap/dist/css/bootstrap.css';
	import App from './App';
	import Home from './components/Home';
	import Profile from './components/Profile';
	import User from './components/User';
	ReactDOM.render(<HashRouter>
	    <App>
				    //一级路由
	        <Route path="/home" component={Home}/>
	        <Route path="/profile" component={Profile}/>
	        <Route path="/user" component={User}/>
	    </App>
	</HashRouter>,document.querySelector('#root'));

	//此组件是由路由渲染的
	import React from 'react';
	import {Route,Link} from 'react-router-dom'
	import UserList from './UserList'
	import UserAdd from './UserAdd'
	export default class User extends React.Component{
	  render(){
	    return (
	      <div className="row">
	        <div className="col-md-2">
	          <div className="nav nav-stacked">
				        //二级路由的导航
	            <li><Link to='/user/list'>用户列表</Link></li>
	            <li><Link to='/user/add'>增加用户</Link></li>
	          </div>
	        </div>
	        <div className="col-md-10">
				        //二级路由
	          <Route path="/user/list" component={UserList}/>
	          <Route path="/user/add" component={UserAdd}/>
	        </div>
	      </div>
	    )
	  }
	}
```
# 路径跳转与路径参数
通过Route渲染的组件，组件实例的props属性上会多出三个路由属性：history，match，loaction；三个值都是对象类型的
**`history`**：提供了很多跳转的方法；
- 1、go
- 2、goBack
- 3、push

**`match`**：包含关于Route与URL匹配的相关信息
- 1、path：路由匹配的路径
- 2、url：当前的路径
- 3、isExact：path和url是否完全匹配，完全匹配时返回true；
- 4、params：默认是空对象，如果Route的path是路径参数的形式，则和url匹配后会产生一个对象,覆盖默认的空对象；

**`loaction`**：存储着与当前页面相关的信息
- 1、hash值
- 2、pathname（路径）
- 3、state（点击路由跳转到当前页面时从路由页面传递过来的数据）

## 路径参数（放在路径中的参数）
当我们需要在路径中放入参数的时候，由于放入的参数是不确定的，所以不能把Route的匹配路径写死，此时就用到了路径参数(类似于正则)；
**`注意`：所有匹配到的参数都会放在match.params的属性上，可以通过this.props.match.params来获取，获取到的值都是字符串类型的**
```javascript
//如果路径写成了路径参数的形式，那么和路径匹配后就会产生一个对象
//下面的路径detail后面的a的值必须要有，前面加了一个' : '意思是a可以代表任何值
 <Route path='/detail/:a' component={Detail}/>
/detail/1  和以上路由匹配后会产生一个对象，可用以下方式获取到:
	this.props.match.params={a:'1'}
//params的属性名来自于Route路径中':'后面的值，属性值来自于客户端路径中对应的值
```
## 如何在路径中加入参数
在**`Link组件`**跳转的时候，直接把需要添加的参数采用模版字符串或者字符串拼接的方式追加到路径后即可
```javascript
let item={id：1}；
<td><Link to={{pathname:`/detail/${item.id}`,}}>跳转到对应路径</Link></td>
```
## withRouter组件
withRouter是一个高阶组件，会把传入的组件的包装成一个Route组件，包装后组件实例的props属性上会多出三个路由属性：history，match，loaction；我们想实现一些如跳转的功能就可以使用这些属性
```javascript
import {withRouter} from 'react-router-dom';
export default withRouter(Nav);
```
## 受保护的路由
当我们需要权限才能通过路由渲染一些组件的时候，我们就需要用到高阶组件的思想，在需要权限才能渲染的组件外面包一层，在包的那层路由中做判断，当有权限时再渲染对应的组件；
```javascript
	import React,{Component} from 'react';
	import ReactDom,{render} from 'react-dom';
	import {HashRouter as Router,Route,Switch,Redirect} from 'react-router-dom';
	import ProtectedRoute from "./containers/ProtectedRoute";
	render(<Router>
	  <App>
	    <Switch>
	      <Route path='/' exact={true} component={Home}/>
     {/*user组件必须有权限才能渲染：localStorage中能获取到login代表登录可以访问user，没有就返回首页，ProtectedRoute 就是我们在外面包的那一层保护路由*/}
	      <ProtectedRoute path={'/user'} component={User}/>
	      <Route path='/detail/:id' component={Detail}/>
	    </Switch>
	  </App>
	</Router>,window.root);

	//ProtectedRoute组件
	import React from 'react';
	import {withRouter} from 'react-router-dom'
	class ProtectedRoute extends React.Component {
	  componentWillMount(){
	    setTimeout(()=>{
	      let login=localStorage.getItem('login');
	      if(!login){
	        //没登录跳转到首页
	        this.props.history.push('/');
	      }
	    },1000);
	  }
	  render(){
	    let {path,component:Component}=this.props;
	    return <Component/>
	  }
	}
	//由于要用到路由的跳转功能所以需要用withRouter包装成路由组件
	export default withRouter(ProtectedRoute);
```