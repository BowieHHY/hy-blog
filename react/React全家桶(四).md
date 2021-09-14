#### 脚手架介绍

- 项目整体技术架构：react + webpack + es6 +eslint 核心

- 创建项目并启动

  ```
  npm install -g create-react-app
  ```

  进入目录 执行命令
  
  这里普及一下：
  
  ```html
  <!-- 开启窗口适配 -->
  <meta name='viewport' ... />
  <!-- 用于配制浏览器页签+地址栏的颜色（仅支持安卓手机浏览器） -->
  <meta name='theme-color' ... />
  <!-- 用于指定网页添加到手机主屏幕后的图标 -->
  <meta name='apple-touch-icon' ... />
  <!-- 应用加壳时的配置文件 -->
  <meta name='manifest' ... />
  ```
  
  ... 此处省略项目细节

- 样式模块化

  `index.module.css`:

  ```css
  .title{
    //....
  }
  ```

  `index.jsx`:

  ```react
  import React, {Component} from 'react'
  import hello from './index.module.css'
  
  export default class Hello extends Component {
    render(){
      return <h2 className={hello.title}>xxxx</h2>
    }
  }
  ```

  这样就不会有命名冲突

- 一个简单的 TodoList 案例 

  > 🔗：https://codesandbox.io/s/react-learn-8vh4m

  - 父子传参数

    子 ---->  父 （method）

    ```react
    // father
    addTodo = (todoObj) => {
      //...
    };
    <Header addTodo={this.addTodo} />
    ```

    ```react
    // son
    handleKeyUp = (event) => {
      //...
      this.props.addTodo({
        id: nanoid(),
        name: target.value,
        done: false
      });
      //...
    };
    ```

    父 ----> 子

    ```react
    // father
    <Header xxx={this.state.xxx} />
    ```

    ```react
    // son
    this.props.xxx
    ```

  - **状态在哪里，操作状态方法就在哪里**

  - 注意区别 defaultChecked 和 checked 的区别，类似的还有 defaultValue 和 value

- 脚手架配置代理 ⚠️

  - client ： localhost:3000   -----> server ： localhost:5000  发出去了但是没有返回回来 （响应拦截了，跨域）

  - 所谓的代理：端口也是3000  把代理请求转发给server, 没有ajax请求，所以没有限制

  - 如何开启？

    - 第一种

      package.json :  所有3000的都转发给5000了

      ```json
      "proxy":"http://localhost:5000"
      ```

      **使用：**

      ```
      axios.get("http://localhost:3000/xxxx")
      ```

      **client 3000 有的资源就不会再转发了** 举个🌰

      ```js
      axios.get("http://localhost:3000/index.html")
      ```

      **说明：**

      1. 优点：配置简单，前端请求资源时可以不加任何前缀。

      2. 缺点：不能配置多个代理。

      3. 工作方式：上述方式配置代理，当请求了3000不存在的资源时，那么该请求会转发给5000 （优先匹配前端资源）

         

    - 第二种 可以配置多个代理

      setupProxy.js   :  commentjs 语法

      ```js
      const proxy = require('http-proxy-middleware')
      
      module.exports = function(app){
        app.use(
        	proxy('/api1',{ // 遇见/api1前缀的请求就触发
            traget:'http://localhost:5000',  //请求转发给谁
            changeOrigin:true, // 控制服务器收到的请求头中host字段的值，false：知道请求还是3000（request.get('host'))
            /*
            	changeOrigin设置为true时，服务器收到的请求头中的host为：localhost:5000
            	changeOrigin设置为false时，服务器收到的请求头中的host为：localhost:3000
            	changeOrigin默认值为false，但我们一般将changeOrigin值设为true
            */
            pathRewrite:{ // 重写路径 (request.url)
              '^/api1':''
            }
          }),
          // proxy(....)
        )
      
      ```

      ***⚠️：如果报proxy错误的，用createProxyMiddleware试一试***

      **⚠️： react version 17.0.2 需要解构 `const {createProxyMiddleware} =require('http-proxy-middleware')`**

      **说明：**

      1. 优点：可以配置多个代理，可以灵活的控制请求是否走代理。
      2. 缺点：配置繁琐，前端请求资源时必须加前缀。
