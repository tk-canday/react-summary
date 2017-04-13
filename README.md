[TOC]

#          <center>React学习总结</center>

## React 全家桶

1. React 主体

2. webpack
3. Flex:布局组件
4. React-Router: 路由组件
5. Redux: View层
6. Mocha: 测试
7. Istanbul: 覆盖率

---

## jsx

> 增强型js语法 —— 将html写在js中

注意:
1.   有且只有一个父元素

2. 模板字符串

  `var div = <div>{ data }</div>`

---

## webpack 配置

1. webpack工具下载
   - `npm install -g webpack`  	  cli环境(脚手架)
- `npm install -g webpack-dev-server`     webpack自带的服务器
- `npm install webpack --save-dev`           webpack本地依赖
- `npm install webpack-dev-server --save-dev`    webpack服务器的本地依赖
2. 依赖库
- babel 转码
    运行时需要用到的包使用--save，否则使用--save-dev
    `--save-dev` 简写 `-D`
+ babel 相关
  + `babel-core -D`       		     babel核心
  + `babel-preset-es2015 -D`      对es2015的预设
  + `babel-preset-react -D`        对react的预设
  + `babel-loader -D`                    babel的加载器
- react
  + `react -D`                           react本身
  + `react-dom -D`                   react-dom本身
  + `react-hot-loader -D`     react热更新加载器
  + `style-loader -D`             style加载器
  + `css-loader -D`                 css加载器(依赖于styleloader)
3. webpack.config.js
```js
module.exports={
  //  入口点
  entry: './index.js',    
  // 出口(编译后放入的目录)
  output: {
    path: __dirname,
    fileName: 'bundle.js'
  },
  // 开发工具(定位错误的)
  devtool:'source-map',   
  /**
   * 用到的模块
   * exclude 排除的文件
   */
  module: {
    loaders: [
        {test:/\.css$/,loader:'style!css'},
        {test:/\.js$/,loader:'react-hot!babel',exclude:/node_modules/},
    ]
  },
};
```
4. .babelrc  babel预设文件
```
{
  presets: [['es2015'],['react']]
}
```

---

## 组件 (component)

### 组件定义

```js
/**
* 组件类首字母必须是大写
* 一个组件中只能有一个顶级标签,例如`h1`
*  constructor 初始化状态(构造函数)
*  super() 继承父类的所有属性和方法(激活的作用)
*  props数据传递 (子组件只能通过 props 来传递数据)
*/
class Comp extends React.Component{
  constructor(...args){
    super(...args)
    this.state={sta:'初始化state'}
  }


  static defaultProps = {prop1:'初始化prop'}

  render () {
      return <p>
          一个组件,name属性值是{this.props.name}
          初始属性是{this.props.prop1}
        </p>
  }
}

// 使用组件
ReactDOM.render(
    <Comp name='"attr"' />,
    document.querySelector('#box')   // 容器
)
```

### 組件的生命周期

1. `componentWillMount()`                    創建之前

2. `componentDidMount()`                      創建完成(在此处添加监听事件)
3. `componentWillUpdate()`                  更新之前(可以监视state变化做一些操作)
4. `componentDidUpdate()`                    更新完成
5. `componentWillUnmount()`                即將銷毀(在此处**一定要**移除监听事件)
6. `componentWillReceiveProps()`      組件參數即将更新（接收到新的props时候调用，可以在此时更新state以响应prop变化）

### 组件的节点( Node)

> 可以用来使用一些原生的方法

* `this.refs`     节点的数组集合
* `ref`                 节点中标识的属性

```jsx
fn(ev){
  // 实现边写边输出
  this.refs.read.innerText = this.refs.write.value
}
render(){
  return  
    <div>
	  <input ref='write' type='text' onChange={this.fn.bind(this)} />
	  <span ref='read' onClick={this.foc.bind(this)}> 
        { this.state.value } 	
      </span>
	</div>;
}
```



### 属性

#### this.props 组件的属性集合

- 属性名规则
  - 驼峰命名
  - 特殊的属性
    - class   ==>   className
    - for      ==>   htmlFor


- 属性扩散
```
var propObj={
  prop1:111,
  prop2:'hello',
  prop3:function(){}
}
render () {
    return <p {...propObj}>相当于把propObj所有属性依次放入p的属性</p>
}
```
#### 属性初始化

```js
static defaultProps = {
  key : value
}
```
#### 属性校验 (propscheck)

- isRequired: 必须有的属性
- 当验证失败时,不会影响渲染,但会控制台警告提示

```js
// 静态定义,也可以跟props一样在组件外部定义
static propTypes = {
  autoPlay: React.PropTypes.bool.isRequired,
  maxLoops: React.PropTypes.func.isRequired,
}
```

#### this.props.children (表示组件的所有子节点)



### 组件间的通信

#### 子组件接受父组件的 数据

1. 父组件给child设置属性
2. 子组件:`this.props.属性名`

#### 子组件向上传递数据

1. `props.中给子组件设置的属性(数据)`
2. 父组件:属性所绑定的函数中接受传递来的数据

```jsx
// 子组件
class Child extends React.Component{
	constructor (...args) {
		super(...args)
    this.num = 5
	}

	render () {
    this.props.po(this.num)
		return <li>{this.props.value}</li>
	}
}

// 父组件
class Ul extends React.Component{
  constructor (...args) {
	super(...args)
  }
  
  static defaultProps = {
    value:'  在内部静态设置props初始值'
  }
  fn(num){
    alert(num)
  }

  render () {
	return (
      <Item po={this.fn.bind(this)} />	
    )
  }
}
```



#### Context 属性跨级传递

如果组件嵌套太多,组件间逐步传递变的繁琐,此时可以用Context
1. 祖组件:
```js
getChildContext:function() {
  return {
    // data
  }
}
// 传给子孙组件的类型校验
childContextTypes: {
  // 校验
}
```
2. 子孙组件
```js
contextTypes: {
  // 校验祖组件的属性
}
3. 子孙组件使用数据
`this.context.属性`
```



### 状态 (state)

> React 以状态为核心

#### 初始化state

```jsx
this.state={
  key: value
}
```

#### this.setState    设置或修改state

```js
this.setState ({
  key: value
}, callback)
```

#### 示例：

```js
/**
 * 边写边输出
 * setState(state对象, callback)    
 * onChange  react的事件(驼峰)
 * this.fn.bind(this) 调用实例的fn方法并绑定给这个元素
 */
class State extends React.Component{
  constructor(...args) {
     super(...args);
     this.state = {value: ''};
  }

  fn(ev){
     this.setState({
       value: ev.target.value
     });
  }

  render(){
     return  <div>
                 <input type='text' onChange={this.fn.bind(this)} />  
                 <span>{ this.state.value }</span>
             </div>;
    }
}

// 渲染组件
ReactDOM.render(
  <State/>,
  document.querySelector('#box')
)
```

#### 问题总结

1. state中的属性不能直接在setState中进行某些运算操作（比如数组的push），

   需要赋值给一个变量进行操作后再重新在setState中赋值

```jsx
this.state = {
  data:[
    {author:'Pete',text:'我是皮特comment'},
  ]
}
this.setState({
  data:this.state.push(....)  // 这么玩会出错，data变成数组的长度，虽然不明原因
})
```





### 动态渲染子组件

> `key`属性: react算法效率问题,且如果没有这个属性的话不能保证渲染的顺序
>
> 应当把key添加到子集数组的子组件本身上，且为了排序正确最好加一个字符串前缀
>
> 注意：`key`不是props，不能当作`props`进行传递

1. 在父亲组件中循环给子组件设置属性 or 子组件中设置属性...
2. 将子组件存入数组 or 子组件接受父组件的数据
3. 在父组件中，遍历子组件数组循环渲染子组件


#### 示例：

```jsx
// 子组件
class Item extends React.Component{
	constructor (...args) {
		super(...args)
	}

	render () {
    this.props.po(this.num)
		return <li>{this.props.value}</li>
	}
}

// 父组件
class Ul extends React.Component{
  constructor (...args) {
	super(...args)
	  this.state = {arr:['item1','item2','item3']}
  }

  render () {
	var arrItem = []
	for (var i = 0; i < this.state.arr.length; i++) {               			  arrItem.push(
        <Item 
          key={'key' + i} 
          value={this.state.arr[i]}                                                                               		  />)
	};

	return (
      <ul>
	    {arrItem}
	  </ul>
    )
  }
}
```



### 表单

- 受限组件

  直接在input中设置 `value='value'`,`checked=true`,

  但是这两个属性是只读的,在React中已经被赋值，prop不能改变

* 不受限表单组件，可以给一个默认值:
  * `defaultValue`属性    初始value,用于text／textarea／select
  * `defaultChecked`属性  初始checked,可用于radio，checkbox


* 受控表单组件:
  `<input value={this.state.value} />`   用state控制表单状态




### 事件

#### 事件的绑定

- 驼峰命名的事件,例如: `onClick`,`onChange`...

- 给事件处理函数传递额外参数的方式：`bind(this, arg1, arg2, ...)`

- 为了保证 `this` 的正确性，一般都需要显式地绑定方法到它的实例上。

  `onClick={this.fn.bind(this)}`

#### 触摸事件

​	如果需要在手机或平板等触摸设备上使用 React，需要在渲染所有组件之前，调用 				`React.initializeTouchEvents(true);` 启动触摸事件处理。

- onTouchStart, onTouchMove, onTouchEnd
- onTouchCancel      touch事件被更高级的事件中断时触发

#### 函数传参

- `onClick={this.fn(arg)}` 这样传参函数会直接调用
- `onClick={() =>this.fn.(arg)}`  应该像这样,return一个函数

#### 阻止冒泡

`event.nativeEvent.stopImmediatePropagation()`

---



## React Route 路由

### packages 可能需要引的包

```
npm install react-router -S       React Router 核心
npm install react-router-dom -S   React Router 的DOM绑定（4.0）
```
### 可能需要引得对象

```js
import { Router, Route, Link, Switch, browserHistory } from 'react-router';
import createBrowserHistory from 'history/createBrowserHistory';
let history = createBrowserHistory();
```
### React Router 

- **<Router />**

  - history = { browserHistory || hashHistory }
  - routes    可以将一个配置路由的数组放入这个属性，以代替Router的路由jsx嵌套

  ~~~jsx
  const routeConfig = [
    {
      path: '/',
      component: App,
      indexRoute: {component: Home},
      childRoutes: [
        {
          path: '/lists',
          component: Lists,
          childRoutes: [
            {
              path: 'list1',
          	component: List1
            },
        	  {
              path: 'list2',
          	component: List2
            }
          ]
        }
      ]
    }
  ]

  React.render( <Router routes={routeConfig} />,document.body )
  ~~~

  ​

  ​

- **<Route path='要匹配的路由'  conponent={ 组件 } ／>**

- **IndexRoute **    默认路由

- **Redirect**           跳转路由

  可以实现路径:  http://localhost:8080/lists/list1

  ​	        变为:  http://localhost:8080/list1

- 示例：

  ```jsx	
  <Router history={browserHistory}>
    <Route path='/' component={ App } >
    	{/* 默认路由 */}
  	<IndexRoute component={ Home } ／>
  	<Route path='/home' component={ Home } />
  	<Route path='/lists' component={ Lists }>
  		<Route path='list1' component={ List1 } />
  		{/* 匹配list1 跳转到 ／list1 */}
  		<Redirect from='list1' to='/list1' />
  	</Route>
    </Route>
  </Router>
  ```

- **<Link  to='路径'> 会解析成a标签 </Link>**

- **IndexLink**        被渲染后才激活指向的路径，与IndexRoute配合使用

#### match 对象 @react-route4.0 

**URL 的有关信息**
<img src="data:image/png;base64,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"/>

+ **isExact** - bool    	   是否完全匹配（是否没有尾随字符）
+ **path** - str                  用于匹配的路径模式
+ **params** - Object      动态路由path的的键值对
+ **url** - str                      匹配到的部分

