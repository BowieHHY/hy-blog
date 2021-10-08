#### 消息订阅与发布

> 🤔 兄弟间组件如何通信？

使用工具： [PubSubJS](https://github.com/mroderick/PubSubJS)

```js
import PubSub from 'pubsub-js'
// or when using CommenJS
const PubSub = require('pubsub-js')
```

订阅消息：收到数据

```react
/** _代表不需要但是又不能忽略的参数 **/
componentDidMount(){
  PubSub.subscribe('name',(_,data)=>{
    console.log(data)
  })
}
```

发布消息：发送数据

```react
PubSub.publish("name",{
	age:18,
	name:'publish'
})
```

取消订阅

```js
componentWillUnmount(){
  // unsubscribe this subscriber from this topic
	PubSub.unsubscribe(token);
}
```



####  Fetch 发送请求

> Windows 内置的 不是第三方库，不用引入，也是Promise封装

```js
try {
  const res = await fetch(`${url}`)
  const data = res.json()
  ...
}catch(err){
  ...
}
```



#### React 路由

> 路由底层是history和hash值实现的 路由就是映射关系(key:value)
>
> https://zhuanlan.zhihu.com/p/116023681

***分类***

1. 后端路由：

   - 理解： value是function, 用来处理客户端提交的请求。

   - 注册路由： router.get(path, function(req, res))

   - 工作过程：当node接收到一个请求时, 根据请求路径找到匹配的路由, 调用路由中的函数来处理请求, 返回响应数据

2. 前端路由：

   - 浏览器端路由，value是component，用于展示页面内容。

   - 注册路由: <Route path="/test" component={Test}>

   - 工作过程：当浏览器的path变为/test时, 当前路由组件就会变为Test组件

**基本使用 react-router**

> 有三个版本 web react-native anywhere
>
> 这里学web react-router-dom

ReactRouterDom.js

```react
import { Link, Route } from "react-router-dom";
import Home from './components/Home'
import About from './components/About'
class xxx extends React.Component{
  ....
  render(){
    return (
      {/** 编写路由链接 **/}
      <Link to="/about">
        About
      </Link>

      <Link to="/home">
        Home
      </Link>
      
      {/** 注册路由 **/}
			<Route path="/about" component={About} />
     	<Route path="/home" component={Home} />
    )
  }
}
```

app.js:

```react
<BrowserRouter>
    <ReactRouterDom />
</BrowserRouter>
```

⚠️ 要说明是那种Router（BrowserRouter or HashRouter）

⚠️ 路由组件放在`pages`文件下

⚠️ 路由组件能收到`props`的东西有

```js
[ // 具体看文档
  history:{},
  location:{},
 	match:{}
]
```

- NavLink 可以实现路由链接的高亮，通过activeClassName指定样式名
- 标签体内容是一个特殊的标签属性
- 通过`this.props.children`可以获取标签体内容

**switch的使用**

```react
<Route path='/home' component={Home} />
<Route path='/home' component={Test} />
```

- 这时候展示哪一个？

  都会匹配 往下匹配

- 如果中间还有其他路由 就会考虑到效率匹配问题

```react
<Switch>
  <Route path='/home' component={Home} />
	<Route path='/home' component={Test} />
</Switch>
```

switch 匹配一个往下就不匹配了（单一匹配）

**解决样式丢失问题**

二级路由：一刷新页面样式会丢失

```react
<Link to="/xxx/about">
  About
</Link>

<Link to="/xxx/home">
 Home
</Link>
```

- 引用的bootstrap.css:

  localhost:3000/css/bootstrap.css

- 如果请求不到资源，就把public下的index.html文件返回给你

  一开始就是访问index.html,里面引入了bootstrap.css; 但是刷新之后会重新请求，意思就是**一刷新** 找不到 localhost:3000/css/bootstrap.css 而是去请求localhost:3000/xxx/css/bootstrap.css

- 解决

  - 解决1

    ```html
    <link rel='stylesheet' href='./css/bootstarp.css'>
    // change 变为绝对路径
    <link rel='stylesheet' href='/css/bootstarp.css'>
    ```

  - 解决2

    适用与react脚手架

    ```html
    <link rel='stylesheet' href='%PUBLIC_URL%/css/bootstarp.css'>
    ```

  - 解决3

    ```react
    // index.js
    import {BrowserRouter} from 'react-router-dom'
    
    // change
    import {HashRouter} from 'react-router-dom'
    ```

**路由的模糊匹配和严格匹配**
