#### 认识

- 关注用户页面
- 不需要大量操作 DOM

#### jsx vs js

```html
// html
<body>
	<div id='test'></div>
</body>

// 顺序不可以改变
<script crossorigin src="https://unpkg.com/react@17/umd/react.production.min.js"></script>
<script crossorigin src="https://unpkg.com/react-dom@17/umd/react-dom.production.min.js"></script>
<script src=''></script>

<script type='text/javascript' src='../js/babel.min.js'></script>
```

```html
// 使用 babel : jsx--->js 
<script type='text/babel'>
  // 渲染虚拟dom
  let VDOM = <h1>hello</h1>
  ReactDOM.render(VDOM,document.getElementById('test'))
</script>
```

```html
// 不使用jsx
<script type='text/js'>
  // 渲染虚拟dom
  let TDOM = ReactDOM.createElement("h1",document.getElementById('test'),"hello")
  ReactDOM.render(VDOM,document.getElementById('test'))
</script>
```

⚠️ 以下很多 都忽略掉`<script>`中的引入

🙋🏼‍♀️ 但是如果要`<h1><span>hello</span></h1>`呢？

不使用jsx的情况下：

```react
ReactDOM.createElement("h1",document.getElementById('test'),React.createElement("span",....))
```

在babel转译下：

```react
function hello() {
  return <div><span>Hello world!</span></div>;
}
// 相当于
function hello() {
  return /*#__PURE__*/React.createElement("div", null, /*#__PURE__*/React.createElement("span", null, "Hello world!"));
}
```

**👉 👉 可知：`jsx` 实际上是`React.createElement(...)` 的语法糖**



#### JSX 语法规则

JSX : react 定义的一种类似于 XML 的 JS 扩展语法 ：XML + JS

> Xml 早期用于存储和传输数据
>
> ```xml
> <student>...</student>
> ```
>
> 后来用 json : JSON.parse()  JSON.stringfy()
>
> "{"name":"Tome"}"

- 定义虚拟DOM 不需要写引号
- 标签中混入**js表达式**是要使用花括号 `{}`
- 样式名为 `className`
- 内联样式 要用 style = {{ key : value }} 的形式去写
- 必须只有一个根标签
- 标签必须闭合
- 如果想要渲染一个 React 组件，名字需要大写开头；若小写，则会转为html中同名元素

```react
const myId = 'VVhsss'
const myData = 'Hello REact'
const arr = [1,3,5,6,7]
const VDOM = (
  <div>
    <h2 className='title' id={myId.toLowerCase()}>
      <span style={{marginRight:'1rem',fontSize:'29px'}}>{myData.toLowerCase()}</span>
    </h2>
    <!-- 能显示 但会报错 the tag <good> is unrecognized in this browser -->
    <good>123</good>
    <!-- React Component -->
    <Good></Good>
    <ul>
    	{
        arr.map((item,index)=>{
          return <li key={index}>{item}</li>
        })
      }
    </ul>
  </div>
)
```

⚠️  什么是表达式 什么是 js语句

1. 表达式会产生一个值，可以放在任何 一个需要值的地方

   a / a+b / demo(1) / arr.map() / function test() {...}

2. 语句（代码） **控制代码走向，没有值**

   if(){...} / for(){...} / switch 



#### 组件和模块

- 模块
  - 理解：向外提供特定功能的js程序 一般就是 一个js文件
  - 作用：复用js ，简化js的编写，提高js运行效率

- 组件
  - 理解：用来实现局部功能效果的代码和资源的集合（html/css/images ...)
  - 作用：复用编码，简化项目编码，提高运行效率

- 模块化

  当应用的js都以模块来编写的，这个应用就是一个模块化的应用

- 组件化

  当应用是以多组件的方式实现，这个应用就是一个组件化的应用

  

#### React 面向组件编程

- 函数式组件

  ```react
  function demo(){
    return (
    	<h2>demo</h2>
    )
  }
  ReactDOM.render(demo,document.getElementById('test'))
  ```

  > 报错：
  >
  > ❌  Warning：Functions are not valid as a React child.

  ```react
  function Demo(){
    return (
    	<h2>demo</h2>
    )
  }
  // 1.执行会执行组件标签 找到Demo组件
  // 2. 发现组件是使用函数定义的 随后调用函数 将返回的虚拟Dom 转为真实Dom 随后呈现在页面中
  ReactDOM.render(<Demo />,document.getElementById('test'))
  ```

  > Demo() 里面的 this 指向谁？
  >
  > ☑️ undefined   babel 编译后开启严格模式

- 类式组件

  > 类里面的构造器 constructor(param1,param2){} paramX是接受实例对象传进来的值

  ```js
  class Person{
    // 构造器方法
    constructor(name,age){
      // this 指的是 实例对象
      this.name = name;
      this.age = age
    }
    // 一般方法
    speak(){
      // speak() 方法在类的原型对象上，供实例使用
      // 通过Person实例调用speak(), speack中的this就是Person实例
      ....
    }
  }
  // 实例对象
  const p1 = new Person('jerry',19)
  p1.speak()
  
  class Student extends Person {
    // 构造器 也可继承父类的
    constrcutor(name,age,grade){
      // 必须调用super. 除非不写构造器
      // 必须最开始调用super()
      super(name,age)
      this.grade = grade
    }
    // 重写从父类继承过来的方法
    speak(){
      ....
    }
  }
  const s1 = new Student('xxx',17,'高一')
  s1.speak()
  ```

  > speak()中的 this 就是实例  ❌

  ```react
  // 类组件
  class MyComponent extends React.Component {
    // 现在可不写构造器
    
    render(){
      return (
      	<div>my component</div>
      )
    }
  }
  // 渲染组件
  
  // 1. 执行会执行组件标签 找到Demo组件
  // 2. 发现组件是使用类定义 new出来该类的实例，并通过该实例调用到原型上的方法
  // 3. 将返回的虚拟Dom 转为真实Dom 随后呈现在页面中
  ReactDOM.render(<MyComponent />,.....)
  ```

  🤔  MyComponent 中 render 是放在哪？ MyComponent的原型对象上 供实例使用

  🤔  实例在哪？React 在渲染的时候帮你new了一个

  🤔  MyComponent 的 this 是 MyComponent **组件实例对象**

  

#### 组件实例的三大核心属性

> 用class定义的组件，因为function定义的this为undefined
>
> 但是最新的hooks也能让function使用

##### state

> 组件的状态里存储着数据，数据驱动页面的展示

```react
class Weather extends React.Component {
  // 需要修改state中的内容
  // 有状态的组件
  constructor(props){
    super(props)
    // 初始化状态
    this.state = { isHot:false }
    // 原型上的changeWeather挂在了实例自身的changeWeather
    this.changeWeather = this.changeWeather.bind(this)
  }
  render(){
    return (
    	<div onClick={changeWeather}>
      	今天天气很{this.state.isHot ? '炎热':'凉爽'}
      </div>
    )
  }
  // changeWeather 放在了原型对象上  供实例使用
  changeWeather(){
    // console.log(this.state.isHot) // 报错error:cannot read property 'state' of undefined
    const isHot = this.state.isHot
    // 严重注意 state不可以直接更改
    // this.state.isHot = !isHot //error
    this.setState({
      isHot:!isHot
    })
  }
}

ReactDOM.render(<Weather />,document.getElementById('test'))

```

⚠️  onClick 只是指定好函数 赋值给onClick

🤔  ？setState 是替换还是合并？

答：合并

🤔  ？ 构造器调用几次？

答：1次

🤔  ？render调用几次？

答：1+n次 n是状态更新的次数

🤔️  ？为什么 `console.log(this.state.isHot)`会报错？？？

```
	 ☑️  通过Weather实例调用changeWeather(), changeWeather中的this就是Weather实例
​	 ☑️   里面的this是undefined 因为只有通过Weather实例调用...(参看第一条)
​	 ☑️  changeWeather 不是通过实例调用的

🌰  ：
study 中打印 this
const p1 = new Person('tom',18)
p1.study() //通过实例调用
const x = p1.study // study属性交给x
x() // 直接调用 undefined
```

> 遇到错误： Cannot read property 'xxx' of undefined
>
> 例如 a.b 即是 a 为undefined 不能读取'xxx'在undefined身上

> 类中所有的局部方法 都开启了严格模式

**❕  答：**
**由于changeWeather是作为onClick的回调,所以不是通过实例	调用的，是直接调用；类中的方法默认开启了局部的严格模式，所	以changeWeather中的this为undefined**

------

简写：

> 类中可以直接写赋值语句
>
> a=1 // yes

```react
class Weather extends React.Component {
  // 构造器也不需要了
  // 有状态的组件
  // 初始化状态
  state = { isHot:false }
  render(){
    return (
    	<div onClick={changeWeather}>
      	今天天气很{this.state.isHot ? '炎热':'凉爽'}
      </div>
    )
  }
  // 赋值语句 + 箭头函数
  changeWeather = () => {
    const isHot = this.state.isHot
    // 箭头函数 找外层函数的this 即Weather的实例对象
    this.setState({
      isHot : !isHot
    })
  }
}

ReactDOM.render(<Weather />,document.getElementById('test'))

```

------

**总结 :**

- state值是对象

- 组件是"状态机"，通过更新组件的state来更新对应的页面展示（重新渲染）

- 组件中的render方法中的this为组件实例对象

- 组件自定义的方法中this为undefined,解决

  a. 强制绑定this:通过函数对象的bind()

  b. 箭头函数

- 状态数据 不能直接修改或更新