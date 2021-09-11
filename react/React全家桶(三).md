#### 组件的生命周期

> 1. 组件从创建到销毁会经历一些特定的阶段
> 2. React 组件中包含一系列钩子函数（生命周期函数），会在特定的时刻调用
> 3. 我们在定义组件时，会在特定的生命周期回调函数中做特定的工作

**【先给出官网的常用的生命周期方法】：**

>  https://zh-hans.reactjs.org/docs/react-component.html#commonly-used-lifecycle-methods

```react
class Life extends React.Component {
  
  state = {
    opacity:0
  }
  
  death = ()=>{
    // 清除定时器 method 1
    // clearInterval(this.timer)
    // 卸载组件
    ReactDOM.unmountComponentAtNode(document.getElementById("test"))
  }
  // 组件挂载完毕
  componentDidMount(){
    this.timer = setInterval(()=>{
      // 获取原状态
      let {opacity} = this.state
      opacity -= 0.1
      if(opacity <= 0) opacity = 1
      // 设置新的透明度
      this.setState({opacity})
    },200)
  }
// 组件将要卸载
	componentWillUnmount(){
    // 清除定时器 method 2
    clearInterval(this.timer)
  }
	// 初始化渲染、状态更新之后
  render(){
    return (
    	<div>
      	<h2 style={{ opacity:this.state.opacity }}>渐变效果</h2>
        <button onClick={this.death}>close</button>
      </div>
    )
  }
}
ReactDOM.render(<Life />,document.getElementById("test"))
```

> 渲染组件到DOM `"挂载（mount）"`
>
> 从DOM中清除组件`"卸载（unmount）"`

- 卸载组件

  `ReactDOM.unmountComponentAtNode(document.getElementById("test"))`

- ⚠️ 报错：（若没有关闭定时器）

  `Warning: Can't perform a React state update on an unmounted component....`

  要及时关闭定时器

##### 图示：⚠️ 旧的生命周期

结合代码看：(箭头表示更新的几个过程)

![image-20210910100347860](/Users/h1/Library/Application Support/typora-user-images/image-20210910100347860.png)

​		*注： 蓝色线路为**更新State**的线路*

​		*注： 蓝色线路为**强制更新**的线路*

```react
class Temp extends React.Component {
  constructor(props){
    console.log("Temp---constructor")
    super(props)
    this.state = {count:0}
  }
  
  add = ()=>{
    const {count} = this.state
    this.setState({
      count:count+1
    })
  }
  
  // 组件将要挂载的钩子
  componentWillMount(){
    console.log('Temp---componentrWillMount')
  }

	// 组件挂载完毕
	componentDidMount(){
    console.log('Temp---componentDidMount')
  }

	// 组件将要卸载的钩子
	componentWillUnmount(){
    console.log('Temp---componentWillMount')
  }

	// 组件是否应该更新 若不写：默认返回true 但写了就必须有返回值
	shouldComponentUpdate(){
    console.log('Temp---shouldComponentUpdate')
    return true
  }

	componentWillUpdate(){
    console.log('Temp---componentWillUpdate')
  }

	componentDidUpdate(){
    console.log('Temp---componentDidUpdate')
  }
  
	force = ()=>{
    // 强制更新
    this.forceUpdate();
  }
  
  render(){
    console.log("Temp---render")
    const {count} = this.state
    return (
    	<div>
      	<span>count:{count}</span>
        <button onClick={this.add}></button>
        <button onClick={this.force}></button>
      </div>
    )
  }
}
```

父组件：

​	1. ....省略

​	2. ⚠️   `componentWillRecieveProps`第一次传的props不算

**对比旧新的生命周期**

> 新的是 cdn 里面有 **@17** 的 就是react新的版本

🤔  为什么更新生命周期钩子函数呢？

> https://zh-hans.reactjs.org/blog/2018/03/27/update-on-async-rendering.html

新的图：

![截屏2021-09-10 下午3.20.27](/Users/h1/Desktop/截屏2021-09-10 下午3.20.27.png)

​	***注：`React更新DOM和refs ` 是旧版本也有的，这里比较细，忽略***

⚠️     在新的版本能用旧的钩子 但是会有**警告**，并且说明**18版本**后一定要变成新的写法才能工作

| 新版本                      | 旧版本                             |
| --------------------------- | ---------------------------------- |
| componentWillMount()        | UNSAFE_componentWillMount()        |
| componentWillUpdate()       | UNSAFE_componentWillUpdate()       |
| componentWillReceiveProps() | UNSAFE_componentWillReceiveProps() |

- ⚠️   getDerivedStateFromProps 了解即可

  若state的值在任何时候都取决于props 那么可以使用

```react
// 接着上面👆的例子

// 不是给实例用的 所以要➕static
// 必须返回状态对象（State Obj 即状态里面的东西）或者 null 
static getDerivedStateFromProps(props){  // ⚠️ 了解即可
  // return null
  
  // count 会一直都是108 会影响状态更新
  // return {count:108}
  
  // 则props会变成状态
  // 此方法适用于罕见的用例，即 state 的值在任何时候都取决于 props
  return props
}
```

- getSnapshotBeforeUpdate 在最近一次渲染输出（提交DOM节点）之前调用 马上更新之前获取信息

  ```react
  getSnapshotBeforeUpdate(){
  	....
    // 需要返回东西 
  	return xxx;
  }
  // 参数都是pre
  componentDidUpdate(preprops,preState，snapshotValue){...}
  ```

  🤔 返回的东西交给谁了？

  👀 返回的东西交给了componentDidUpdate

  🤔 使用场景

  🌰 

  ```react
  /**
  .list{
    height:150px;
    overflow:auto;
  }
  .news{
    height:30px;
  }
  **/
  class List extends React.Component{
    state = {newsArr:[]}
  	componentDidMount(){
      setInterval(()=>{
        // 获取原状态
        const {newsArr} = this.state
        // 模拟一条新闻
        const news = '新闻' + (newsArr.length+1)
        // 更新状态
        this.setState({newsArr:[news,...newsArr]})
      },1000)
    }
  
  	getSnapshotBeforeMount(){
      return this.refs.list.scrollHeight;
    }
  
  	componentDidUpdate(prePorps,preState,height){
      this.refs.list.scrollTop += this.refs.list.scrollHeight - height
    }
  
  	render(){
      return (
      	<div className="list" ref='list'>
        	{
            this.state.newsArr.map((n,index)=>{
              return <div key={index} className="news">{n}</div>
            })
          }
        </div>
      )
    }
  }
  ```

**🎉总结**

- 初始化阶段：由ReactDOM.render()触发 --- 初次渲染
  - constructor
  - getDerivedStateFromProps
  - render()
  - componentDidMount()

- 更新阶段：由组件内部this.setState()或者父组件重新render渲染
  1. getDerivedStateFromProps()
  2. shouldComponentUpdate()
  3. render()
  4. getSnapshotBeforeUpdate()
  5. componentDidMount()

- 卸载组件 ：由ReactDOM.unmountComponentAtNode() 触发
  1. componentWillUnmount()
