[toc]
# React 全家桶
  1. React 主体
  2. webpack
  3. Flex:布局
  4. React-Route: 路由
  5. Redux: View层
  6. Mocha: 测试
  7. Istanbul: 覆盖率

# jsx
    增强型js语法 —— 将html写在js中
注意:
  1. 有且只有一个父元素
  2. 模板字符串
    `var div = <div>{ data }</div>`

# webpack 配置
1. webpack工具下载
 - `npm install -g webpack`   cli环境(脚手架)
 - `npm install -g webpack-dev-server`    webpack自带的服务器
 - `npm install webpack --save-dev`       webpack本地依赖
 - `npm install webpack-dev-server --save-dev`    webpack服务器的本地依赖
2. 依赖库
- babel 转码
    运行时需要用到的包使用--save，否则使用--save-dev
    `--save-dev` 简写 `-D`
  + `babel-core -D`       babel
  + `babel-preset-es2015 -D`  对es2015的预设
  + `babel-preset-react -D`  对react的预设
  + `babel-loader -D`     babel的加载器
- react
  + `react -D`      react本身
  + `react-dom -D`  react-dom本身
  + `react-hot-loader -D`   react热更新加载器
  + `style-loader -D`       style加载器
  + `css-loader -D`         css加载器(依赖于styleloader)
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

# 组件 (component)
##　组件定义
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

## 組件的生命周期
   1. `componentWillMount()`        創建之前
   2. `componentDidMount()`         創建完成(在此处添加监听事件)
   3. `componentWillUpdate()`       更新之前
   4. `componentDidUpdate()`        更新完成
   5. `componentWillUnmount()`      即將銷毀(在此处移除监听事件)
   6. `componentWillReceiveProps()` 組件參數即将更新

## 组件的节点( Node)
    可以用来进行原生操作
* `this.refs`     节点的数组集合
* `ref`      节点中标识的属性

## 属性
### this.props 组件的属性集合
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
### 属性校验 (propscheck)
* isRequired: 必须有的属性
* 当验证失败时,不会影响渲染,但会控制台警告提示
```js
// 静态定义,也可以跟props一样在组件外部定义
static propTypes = {
  autoPlay: React.PropTypes.bool.isRequired,
  maxLoops: React.PropTypes.func.isRequired,
}
```
### 属性初始化
```js
getDefaultProps : function () {
    return {
      title : 'Hello World'
    };
  },
```
### this.props.children (表示组件的所有子节点)


## 组件间的通信
### 子组件接受父组件的 数据
1. 父组件给child设置属性
2. 自组件:`this.props.属性名`

### 子组件向上传递数据
1. `props.中给子组件设置的属性(数据)`
2. 父组件:属性所绑定的函数中接受传递来的数据

### Context 属性跨级传递
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

## 循环渲染子组件
`key`属性: 效率问题,且如果没有这个属性的话不能保证渲染的顺序
1. 在父亲组件中循环给子组件设置value,属性,key or 子组件中设置属性...
2. 将子组件存入数组 or 子组件接受父组件的数据
3. 在父组件中嵌入数组渲染 or 子组件内部map遍历生成元素


## 状态 (state)
React 以状态为核心
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

## 表单
直接在input中设置 `value='value'`,`checked=true`的话,这两个属性是只读的,因为prop不能改变,
* 可以使用默认值属性:
- `defaultValue`属性    初始value,可以修改
- `defaultChecked`属性  初始checked,可以修改
* 推荐受控表单组件:
`<input value={this.state.value}/>`   用state控制表单状态


## 事件
  驼峰命名的事件,例如: `onClick`,`onChange`...
### 事件的绑定  
给事件处理函数传递额外参数的方式：`bind(this, arg1, arg2, ...)`
`onClick={this.fn.bind(this)}`

### 函数传参
- `onClick={this.fn(arg)}` 这样传参函数会直接调用
- `onClick={() =>this.fn.(arg)}`  应该像这样,return一个函数

### 阻止冒泡
`e.nativeEvent.stopImmediatePropagation()`

# 显示隐藏动画
1. `npm install react-addons-css-transition-group -save`
2. 在`<ReactCssTransitionGroup/>`组件中配置属性
