

#### 组件实例的三大核心属性（续）

> 引入的 react、react-dom、babel参考上一篇

##### props

```react
class Person extends React.Component{
  render(){
    const {name,age} = this.props
    return (
      <ul>
      	<li>name: { name }</li>
        <li>age: { age }</li>
      </ul>
    )
  }
}
ReactDOM.render(<Person name='tom' age="18" />,document.getElementById('test'))
```

**简写 :**

```react
const p = {name:'xxx',age:18}
ReactDOM.render(<Person {...p} />,document.getElementById('test'))
```

⚠️    展开不能展开对象，`{...xxx}`是复制一个对象。但是react中的`{...p}`是因为`react+babel`允许你扩展一个对象

🤔    如果要传数字呢?

```react
ReactDOM.render(<Person name='tom' age={18} />,document.getElementById('test'))
```

**标签属性限制 propTypes**

- 类型限制
- 必要性限制
- 默认值限制

```react
// 15.xxxx版本前 React.PropTypes 还在维护
// 16 版本后就不这样写了 直接PropTypes
// 需要引入

// isRequired 为必须性
<script src='../js/prop-tyoes.js'></script>
Person.propTypes = {
  name : PropTypes.string.isRequired,
  age : PropTypes.number,
}
// 默认值
Person.defaultProps={
  age : 18
}
```

如果限制函数呢？

```react
Person.propTypes = {
	speak: PropTypes.func
}
```

> ⚠️   `props` 是只读的

 **props 的简写方式**

> 给类本身加属性 static

```react
class Person extends React.component {
	...
	static propTypes = {...}
	static defaultProps = {...}

	render(){...}
}
```

**构造器**

```react
class Person extends React.component {
	constructor(props){
		super(props)
    console.log(this.props) //可以拿到
	}
}
```

🤔   为什么要给 super 呢？传不传给 super 有什么区别呢？

如果不传值，在构造器中不能通过 `this.props`拿到值

构造器是否接收props,是否传递给super,取决于：是否希望在构造器中通过this访问props

**函数式组件使用props**

> 之前也提过 函数中访问不了this(这里不说hooks)  玩不了state & refs 但是props🉑️

```react
function Person(props){
  const { name,age } = props
  return (
  	...
  )
}
Person.propsTypes = {...}
Person.defaultProps = {...}
ReactDOM.render(<Person name='tom' age=18 />,document.getElementById('test'))
```



##### refs & 事件处理

**字符串ref**

> 后期不推荐使用： 因为存在一些问题
>
> 总结来说就是效率问题

```react
class Demo extends React.Component{
  showData = ()=>{
    const {input1} = this.refs
    alert(input1.value)
  }
  showData2 = ()=>{
    const {input2} = this.refs
    alert(input2.value)
  }
  render(){
    return (
    	<div>
      	<input ref='input1' type='text' placeholder='点击按钮提示数据' />
        <button onClick={this.showData}>click</button>
        <input onBlur={this.showData2} ref='input2' type='text' placeholder='失去焦点提示数据' />
      </div>
    )
  }
}
ReactDOM.render(<Demo />,document.getElementById('test'))
```

⚠️   Demo 实例中的refs存着多组ref节点

⚠️   `this.refs.input1` 拿到的是真正的节点Dom

**回调ref**

接收的参数就是ref当前所在的节点

```react
class Demo extends React.Component{
	...
	 render(){
    return (
    	<div>
      	<input ref={ currentNode => this.input1 = currentNode } type='text' placeholder='点击按钮提示数据' />
      ....
      </div>
    )
  }
}
```

**回调ref回调执行次数问题**

如果 `ref` 回调函数是以内联函数的方式定义的，在更新过程中它会被执行两次，第一次传入参数 `null`，然后第二次会传入参数 DOM 元素。这是因为在每次渲染时会创建一个新的函数实例，所以 React 清空旧的 ref 并且设置新的。通过将 ref 的回调函数定义成 class 的绑定函数的方式可以避免上述问题，但是大多数情况下它是无关紧要的。

JSX 写注释

```react
...

saveInput = ()=>{
  ...
}
  
...

{/* <input ref={ currentNode => this.input1 = currentNode } type='text' placeholder='点击按钮提示数据' /> */}
<input ref={ this.saveInput } type='text' placeholder='点击按钮提示数据' />
```

**createRef api**

> React.createRef 调用后可以返回一个容器，该容器可以存储被ref 所标识的节点
>
> 该容器是“专人专用的” 只能存一个

```react
class Demo extends React.Component{
 ...
 myRef = React.createRef()
 myRef2 = React.createRef()
 showData = ()=>{
    const {input1} = this.refs
    alert(input1.current.value)
  }
}
<input ref={ this.myRef } type='text' placeholder='点击按钮提示数据' />
```

**事件处理**

- 通过onXxx 属性指定事件处理函数（注意大小写）
  - React 使用的是自定义（合成）事件，而不是使用的原生DOM事件   -------为了更好的兼容性
  - React 中的事件是通过事件委托方式处理的（委托给组件最外层的元素）-------为了高效

- 通过event.target得到发生事件的DOM元素对象（不过度使用ref

  ）

**收集表单数据**

```react
class Login extend React.Component{
  
  handleLogin = (event) => {
    // 阻止表单提交
    event.preventDefalut()；
    const {username,password} = this
    alert(`username: ${username.value},password:${password.value}`)
  }
  
  render(){
    return (
    	<form action='http://www.baidu.com' onSubmit={this.handleLogin}>
      	username : <input ref={c=>this.username = c } type='text' name='username' />
        password : <input ref={c=>this.password = c} type='password' name='password' />
        <button>login</button>
      </form>
    )
  }
}
ReactDOM.render(<Login />,document.getElementById('test'))
```

🤔  受控组件和非受控组件的区别？

- 非受控组件

  现用现取(如上 👆 )

- 受控组件

  随着输入拿到输入值，存取状态（有点像vue的双向数据绑定）

  不需要使用 ref

  ```react
  state = {
    username:''
  }
  saveUsername = (event)=>{
    ...
    alert(event.target.value)
    this.setState({
      username: event.target.value
    })
  }
  <input onChange={this.saveUsername} type='text' name='username' />
  ```

  

##### 补充 ： 高阶函数 & 函数柯里化

简化👆代码：

```react
saveFormData(dataType){
  return (event)=>{ // 返回值不是undefined
    this.setState({
      [dataType]:event.target.value
    })
  }
}

// error 这是把saveFormData方法的返回值 给onChange, 而返回的是undefined (在saveFormData不返回东西的情况下)
username : <input onChange={this.saveFormData('username')}  type='text' name='username' />
```

- 高阶函数

  如果一个函数符合下面2个规范中的任何一个，那该函数就是高阶函数

  - 若 A 函数，接收的参数是一个函数，那么 A 就可以称之为高阶函数
  - 那么 A 就可以称之为高阶函数若 A 函数，调用的返回值依然是一个函数
  - 常见：promise 、setTimeout 、 arr.map 等

- 函数的柯里化： 通过函数调用继续返回函数的方式，实现多次接受参数最后统一出的函数编码形式

💡 tips : 可以折叠代码

```react
//#region
	/*
	*/
//#endregion
```

还有一种简化：

```react
saveFormData(dataType,value){
 ...
}

username : <input onChange={event=>this.saveFormData('username',event.target.value)}  type='text' name='username' />
```

