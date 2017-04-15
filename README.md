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

3. { javascript 代码写在大括号中 }

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

1. `componentWillMount()`                    组件挂载之前（建议在这进行异步操作）
2. `componentWillReceiveProps()`      組件属性即将更新（可以在此时更新state以响应prop变化）父组件发生render的时候子组件会发生此事件 || 接收到新的props时候调用，需要验证
3. `componentDidMount()`                      组件挂载完成，一个周期内只会调用一次(在此处注册监听事件)
4. `shouldComponentUpdate`                  每次调`setState`都会调用此事件判断是否需要从新渲染，默认返回true。
5. `componentWillUpdate()`                  组件更新之前，当`shouldComponentUpdate`返回true或者调用`forceUpdate` 后被调用(可以监视state变化做一些操作)
6. `componentDidUpdate()`                    除了首次render后调用`componentDidMount()`，其他render 结束之后都是调用此方法
7. `componentWillUnmount()`                即將銷毀(在此处**一定要**移除注册的事件)

示例：

```Max
componentWillReceiveProps(nextProps) {
    if (nextProps.data.count) {
      this.setState({
        count : this.props.data.count
      })
    }
  }
```

> 问题：
>
> 执行顺序



#### render

- 触发render的途径：
  - 首次渲染
  - 调用`this.setState`
  - 父组件发生更新（不管props有没有发生变化）
  - 调用 `this.forceUpdate` 

<img src="data:image/png;base64,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"/>

### 组件的节点( Node)

> 可以用来使用一些原生的方法

- `findDomNode（ref || node）`  返回当前挂载的某个节点

* `this.refs`     节点的数组集合（组件挂载完毕之后才可以使用）
* `ref`                 节点中标识的属性

```jsx
fn(ev){
  // 实现边写边输出
  this.refs.read.innerText = this.refs.write.value
}
render(){
  return  
    <div>
	  <input ref='write' type='text' 
        onChange={this.fn.bind(this)} 
      />
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
    - dangerouslySetInnerHtml    (会将传入的值，进行执行)


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

#### 问题总结：

1. props不能再组件内修改
2. key不属于props



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
// 传给子孙组件的数据类型校验 @ES6 
static childContextTypes = {
    // 属性校验
}

// 要传的数据
getChildContext() {
  return {
     key: value
  }
}
```
2. 子孙组件
```js
static contextTypes = {
  // 校验祖组件的属性 @ES6
}

// 子孙组件使用数据
this.context.属性key
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

#### forceUpdate   强制更新组件

#### 问题总结:

1. 在state初始化中访问 props：需要这样：

此时，组件还未构建出来，需要在arguments里获取props

```js
class Comp extends React.Component{
  constructor(...args){
    super(...args)
    this.state = {
      count: args.props.count
    }
  }
}
```

2. 每次进行`setState`操作，都会进行组件更新，不论oldValue 与 newValue是否有变化，所以避免在除了`componentWillReceiveProps`的周期函数中对state进行操作，可能会形成死循环；

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
	}

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

  直接在input中设置 `value='value'` , `checked=true`,

  但是这两个属性是只读的,在React中已经被赋值，prop不能改变

* 不受限表单组件，可以给一个默认值:
  * `defaultValue`属性    初始value,用于text／textarea／select
  * `defaultChecked`属性  初始checked,可用于radio，checkbox


* 受控表单组件:
  `<input value={this.state.value} />`   用state控制表单状态


> 问题：需要在初始时候定义一个需要绑定的数据，否则初始完成后是undefined，会变成受限组件



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
npm install react-router-dom -S   React Router 的DOM绑定（@react-router 4.0）
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

- **`<Route path='要匹配的路由'  conponent={ 组件 } ／>`**

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

- **`<Link  to='路径'> 会解析成a标签 </Link>`**

- **IndexLink**        被渲染后才激活指向的路径，与IndexRoute配合使用

#### match 对象 @react-route4.0 

**URL 的有关信息**
<img src="data:image/png;base64,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"/>

+ **isExact** - bool    	   是否完全匹配（是否没有尾随字符）
+ **path** - str                  用于匹配的路径模式
+ **params** - Object      动态路由path的的键值对
+ **url** - str                      匹配到的部分

