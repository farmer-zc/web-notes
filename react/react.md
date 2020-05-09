# 1react 知识点总结

## 1. 组件（命名首字母必须大写）
> 组件，从概念上类似于 JavaScript 函数，他接受一个参数“props”，并且返回用于描述页面内容的react元素

### 1.1 函数组件
```react
// 1. 定义最简单的组件就是函数组件(本质上就是JavaScript函数)
// 2. 函数组件接受唯一带有数据的“props”
// 3. 函数组件中没有生命周期，但是可以通过 HOOK 实现
function Comp (props) {
	return <h1>hello, {props.name}</h1>
}
```
### 1.2 class 组件
```react
import React from 'react'
class Comp extends React.Component {
	// ... 生命周期
	render () {
		return <h1>hello, {this.props.name}</h1>
	}
}
```
## 2. state  &  props
> props：用于接收父组件传递的属性，是只读的不能修改
> state：state是组件内部维护的状态是私有的，并且完全受控于当前组件（用 setState() 更新）
> state 是私有的，其他组件无法访问，但是组件可以选择把他的 state 作为 props向下传递到它的子组件

```react
class Comp extends React.Component {
	constructor (props) {
		super(props)
		this.state = {
			name: 'react'
		}
	}
	componentDidMount() {
		// 使用 setState() 来更新组件state
		setTimeout(()=>{
			this.setState({
				name: 'hello react'
			})
		}, 1000)
	}
	render () {
		return (
			<div>
				<h1>{this.props.name}</h1>
				<h1>{this.state.name}</h1>
			</div>
		)
	}
}
```
### 2.1 state使用注意事项
1. 不能直接修改state，应该使用setState()
```react
this.state.name = 'hello'; // 错误
this.setState({name: 'hello'}) // 正确
```
2. setState 的更新是异步的，此时可以让 setState 接收一个函数而不是对象
```react
	// 用上一个state作为第一个参数，将此次更新被应用是的props做第二个参数
    this.setState((state, props) => ({
    	name: state.name + props.name
    }))
```
### 2.2 props使用
**this.props.children 是一个特殊的 prop，通常由 JSX 表达式中的子组件组成，而非组件本身定义**
```react
// 父组件
function ParComp () {
    let comp= "<h2>我是通过prop传递的第二种方式</h2>"
	return (
		<div>
			<ChildComp name='react' comp={comp}>
				<h1>我是类似 vue 插槽,放在props.children中的</h1>
			</ChildComp>
		</div>
	)
}
// 子组件
function ChildComp (props) {
	return (
		<div>
			<h1>我是逐渐本身就有的jsx</h1>
        	<p>我是props上的属性name：{props.name}</p>
			{props.comp}
			{props.children}
		</div>
	)
}

```

## 3. 组件的生命周期(只展示常用)
```react
// 常用的生命周期方法
// 1. render
// 2. constructor
// 3. componentDidMount
// 4. componentDidUpdate
// 5. componentWillUnmount
class Comp extends React.Component {
	// 组件挂载-----------------------------------------
	// 构造函数，如果不需要初始化state或不进行方法绑定，可以不实现
	// 如果实现，要在其他语句前调用 super(props) 否则 this.props 会未定义
	// 作用：1. 初始化内部state  2. 为事件处理函数绑定实例
	constructor(props) { 
		super(props)
		// 不要在构造函数中调用 setState()
		// 不能将props的值赋值给state
		this.state = { name: 'react' };
		this.clickFn = this.clickFn.bind(this);
	}
	// 会在调用 render 方法前调用,并且在挂在和更新时都会被调用
	// 他应该返回一个对象来更新state，如果返回null 则不更新任何内容
	static getDerivedStateFromProps(props, state) {}
	
	// class组件中唯一必须实现的方法
	// 如果 shouldComponentDidMount 返回 false 则不会调用
	render () { }
	// 会在组件挂在后立即调用，
	// 依赖于DOM节点的初始化应该放在这里，例如ajax请求
	// 这里是比较适合添加订阅的地方，但不要忘记在 componentWillUnmount 里取消订阅
	// 你可以在 componentDidMount() 里直接调用 setState()，但此渲染会发生在浏览器更新屏幕之前
	componentDidMount () {}
	
	// 组件更新------------------------------------------
	// static getDerivedStateFromProps(props, state) {}
	
	// 根据 shouldComponentUpdate 返回值，判断是否重新渲染，默认是true
	// 当props或state发生变化时，shouldComponentUpdate会在渲染执行前被调用
	// 此方法仅作为性能优化，应该考虑使用内置的 PureComponent 组件
	shouldComponentUpdate(nextProps, nextState) { }
	
	// render () {}
	
	// 在最近一次渲染输出前调用，它使得组件能在发生更改之前从 DOM 中捕获一些信息（例如，滚动位置）。此生命周期的任何返回值将作为参数传递给 componentDidUpdate()
	getSnapshotBeforeUpdate() { }
	
	// 会在组件更新后立即调用，首次渲染不会调用此方法
	// 当组件更新后，可以在此处对 DOM 进行操作。如果你对更新前后的 props 进行了比较，也可以选择在此处进行网络请求
	componentDidUpdate (prevProps) {
	  // 典型用法（不要忘记比较 props）：
      if (this.props.userID !== prevProps.userID) {
        this.fetchData(this.props.userID);
      }
	}
	
	// 组件卸载-----------------------------------------------------------
	// 会在组件卸载之前直接调用，可以做一些清理操作，如定时器，订阅等
	// 不能调用setState() 因为该组件永远不会在渲染
    componentWillUnmount () { }
    
    // 错误处理------------------------------------------------------
    static getDerivedStateError(){}
    
    componentDidCatch() {}
	
}
```

### 3.1  render
> class组件中唯一必须实现的方法
> 如果 shouldComponentDidMount 返回 false 则不会调用

```react
render() {}
```

### 3.2  constructor 
> 如果不需要初始化state或不进行方法绑定，可以不实现
> 如果实现，要在其他语句前调用 super(props) 否则 this.props 在构造函数中会未定义
> 作用：1. 初始化内部state  2. 为事件处理函数绑定实例

```react
	constructor(props) { 
		super(props)
		// 不要在构造函数中调用 setState()
		// 不能将props的值赋值给state
		this.state = { name: 'react' };
		this.clickFn = this.clickFn.bind(this);
	}
```
### 3.3  componentDidMount
> 会在组件挂在后立即调用，
> 依赖于DOM节点的初始化应该放在这里，例如ajax请求
> 这里是比较适合添加订阅的地方，但不要忘记在 componentWillUnmount 里取消订阅
> 你可以在 componentDidMount() 里直接调用 setState()，但此渲染会发生在浏览器更新之前

```react
componentDidMount () {}
```
### 3.4  componentDidUpdate
> 会在组件更新后立即调用，首次渲染不会调用此方法
> 可以在此处对 DOM 进行操作。如果你对更新前后的 props 进行了比较，也可以选择在此处进行网络请求

```react
componentDidUpdate (prevProps) {
    // 典型用法（不要忘记比较 props）：
    if (this.props.userID !== prevProps.userID) {
    	this.fetchData(this.props.userID);
    }
}
```
### 3.5  componentWillUnmount
> 会在组件卸载之前直接调用，可以做一些清理操作，如定时器，订阅等
> 不能调用setState() 因为该组件永远不会在渲染

```react
componentWillUnmount () { }
```
## 4. 事件处理
> React 事件的命名采用小驼峰式（camelCase），而不是纯小写。
> 使用 JSX 语法时你需要传入一个函数作为事件处理函数，而不是一个字符串。
> 不能通过返回 false 的方式阻止默认行为。你必须显式的使用 preventDefault
> e 是一个合成事件。React 根据 W3C 规范来定义这些合成事件，不需要担心跨浏览器的兼容性

```react
function ActionLink() {
  function handleClick(e) {
    e.preventDefault();
    console.log('The link was clicked.');
  }

  return (
    <a href="#" onClick={handleClick}>
      Click me
    </a>
  );
}
```
### 4.1 JSX回调函数中的 this
> class 的方法默认不会绑定 this。如果你忘记绑定 this.handleClick 并把它传入了 onClick，当你调用这个函数的时候 this 的值为 undefined。
**绑定 this 的4种方法**
```react
class Comp extends React.Component {
	constructor (props) {
		super(props);
		this.state = {
			isOpen: true
		}
		// 第一种: 在构造函数中绑定this
		this.handleClick = this.handleClick.bind(this)
	}
	
	handleClick () {
		this.setState(state => ({
			isOpen: !state.isOpen
		}))
	}
	// 第二种方式 class fields 语法
	_handleClick = () => {
		this.setState(state => ({
			isOpen: !state.isOpen
		}))
	}
	
	render () {
		return (
			<div>
				第一种方式： 回调函数中绑定 this
				<button onClick={this.handleClick}>
                    {this.state.isOpen ? 'ON' : 'OFF'}
                <button>
                第二种方式：class fields 语法
                <button onClick={this._handleClick}>
                    {this.state.isOpen ? 'ON' : 'OFF'}
                <button>
                第三种方式：箭头函数的方式
                <button onClick={()=> { this.handleClick() }}>
                    {this.state.isOpen ? 'ON' : 'OFF'}
                <button>
			</div>
		)
	}
}
```
### 4.2 参数传递
> 通过箭头函数的方式，事件对象 e 必须显示的进行传递
> 通过bind的方式，时间对象会隐式的进行传递
```react
// 下面两种方式是一样的，事件对象 e 会被作为第二个参数传递
<button onClick={(e) => this.deleteRow(id, e)}>Delete Row</button>
<button onClick={this.deleteRow.bind(this, id)}>Delete Row</button>
```

## 5. jsx 条件渲染

### 5.1 if   else

> React 中的条件渲染和 JavaScript 中的一样，使用 JavaScript 运算符 [`if`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/if...else) 或者[条件运算符](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Operators/Conditional_Operator)去创建元素来表现当前的状态，然后让 React 根据它们来更新 UI

```
// 第一种方式
function Comp (props) {
	if(props.isLogin) {
		return (<div>已经登陆</div>)
	}else{
		return (<div>请登录</div>)
	}
}
// 第二种方式
class Comp extends React.Component {
	render () {
		let loginDom;
		if (this.props.isLogin){
			loginDom = '<div>已经登陆</div>';
		} else {
			loginDom = '<div>请登录</div>';
		}
		return (
			<div>
				{ loginDom}
			</div>
		)
	}
}

```

### 5.2 与运算符 &

> 通过花括号包裹代码，你可以[在 JSX 中嵌入任何表达式](https://react.docschina.org/docs/introducing-jsx.html#embedding-expressions-in-jsx)。这也包括 JavaScript 中的逻辑与 (&&) 运算符

```react
function Comp (props) {
	return (
        <div>
        	{props.isShow && <h1>这是要显示的内容</h1>}
        </div>
    )
}
```

### 5.3  三目运算符

> 另一种内联条件渲染的方法是使用 JavaScript 中的三目运算符 [`condition ? true : false`](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Operators/Conditional_Operator)。

```react
function Comp (props) {
	return (
		<div>
        	{props.isLogin ? <h1>已经登陆</h1> : <h1>请登录</h1>}
        </div>
	)
}
```

## 6. 列表渲染 & key

```react
function Comp (props) {
	const numberArr = [ 1, 2, 3];
	const listItem = numberArr.map(num => (
		<li key={num}>{num}<li>
	) )
	return (
            <ul>{listItem}</ul>
	)
}
```

### 6.1 key

> 1. key帮助 react 识别哪些元素变化了，比如被添加或者删除，因此应该给每一个元素赋予一个唯一的标识
> 2. 元素的 key 最好是这个元素在列表中拥有的一个独一无二的字符串。

### 6.2 在jsx中嵌入map（）

> jsx允许在 { } 中嵌入任意 js 表达式

```react
function Comp (props) {
	let numberArr = [1, 2, 3];
	return (
		<ul>
			{
				numberArr.map((num, index) => (
					<li key={index}> 
						{ num } 
                    </li>
				))
			}
		</ul>
	)

}
```

## 7. 表单(受控组件，双向数据绑定)

> 渲染表单的 React 组件还控制着用户输入过程中表单发生的操作。被 React 以这种方式控制取值的表单输入元素就叫做“受控组件”。

###  7.1  input

```react
class Comp extends React.Component {
	constructor (props) {
		super(props);
		this.state = { value: '' }
	}
	handleChange (e) {
		this.setState({
            value: e.target.value
        })
	}
	render () {
		return (
			<div>
                <p>{this.state.value}</p>
            	<input type="text" 
                    value={this.state.value} 
                    onChange={() => this.handleChange()} />
            </div>
		)
	}
}
```

### 7.2  处理多个输入

> 当需要处理多个 `input` 元素时，我们可以给每个元素添加 `name` 属性，并让处理函数根据 `event.target.name` 的值选择要执行的操作

## 8. 父子组件间通信

### 8.1  props（父 > 子）

```react

function ParComp () {
	retrun (
        <div>
            <h1>我是父组件内容</h1>
            <ChildComp message="我是传递的message" />
        </div>
	)
}
function ChildComp (props) {
    return (
    	<div>
            <h1>我是子组件，父组件传递message为： {props.message}</h1>
        </div>
    )
}

```

###  8.2 状态提升（子 > 父）

当多个组件共享同一个数据的时候，需要用到状态提升，即将多个子组件需要共享的数据存在共同的父组件中，然后通过props获取父组件数据（即多个子组件中原本相通的state  都提升到父组件的 state中，然后通过 props 传递给每个子组件，并将setState的方法也传递给子组件）

```react
class ParComp extends React.Component {
	constructor (props) {
        super(props);
        this.state = {val: ''};
	}
    setVal = val => {
        this.setState({count: val})
    }
    
    render () {
        return (
        	<div>
                <p>{this.state.val}</p>
            	<ChildComp val={this.state.val} setVal={this.setVal} />
            </div>
        )
    }
}

class ChildComp extends React.Component {
    setVal (e) {
        this.props.setVal(e.target.value)
    }
    render () {
        return (
            <input type="text" value={this.props.value} 
                   onChange={ e => this.setVal(e) }"/>
        )
    }
}
```

## 9. refs

Refs 提供了一种方式，允许我们访问 DOM 节点或在 render 方法中创建的 React 元素

> 有些场景需要获取某一个真实的DOM元素来交互，此时就需要ref
>
> ref 还可以写在 class 组件上，则回调函数获取的是改组件的实例
>
> 函数组件不能使用ref ， 因为函数组件没有实例，但是在函数组件内部可以使用ref

### 9.1 给 DOM 元素添加 ref 

> 给 DOM 元素添加 ref 属性，该属性接受一个回调函数，该函数接受底层的 DOM 元素作为回调函数的参数

```react
class IptComp extends React.Component {
    handle () {
        // 打印出 input 输入框的值
        console.log(this.eleIpt.value)
    }
    render () {
        <div>
        	<input type="text" ref={el => {this.eleIpt = el}} />
            <button onClick = {() => this.handle() }>点击</button>
        </div>
    }
}
```

### 9.2 给 class 组件上添加 ref

> 给 class 组件添加 ref 属性，则回调函数则是已经加载的该组件的实例

```react
class ChildComp extends React.Component {
    sayHello () {
        alert('hello');
    }
	render () {
		return (
        	<div> </div>
        )
	}
}

class ParComp extends React.Component {
    sayHello() {
        this.compELe.sayHello();
    }
	render () {
        retrun (
        	<div>
            	<ChildComp ref={el => this.compEle = el}>
                <button onClick={() => this.sayHello()}>点击</button>
            </div>
        )
    }
}
```

### 9.3 对父组件暴露DOM子节点

> 有些情况下，我们需要从父组件中访问子组件中的某一个DOM节点。可以通过在组件上添加自定义属性，传递一个回调给子组件

```react
class ChildComp extends React.Component {

    render () {
        return (
            <div>
                <input type='text' ref={this.props.inputEle}></input>
            </div>
        )
    }
}

class ParComp extends React.Component {

    getValue () {
        console.log(this.inputEle.value)
    }

    render () {
        return (
            <div>
                <ChildComp inputEle={el => this.inputEle = el}></ChildComp>
                <button onClick={() => this.getValue()}>点击</button>
            </div>
        )
    }
}
```

### 9.4 使用 React.createRef() 创建

> <font color=red>**注意要通过 current 来访问 DOM 节点**</font>

```react

import React, { Component } from 'react'
export default class App extends React.Component {
  constructor(props) {
    super(props);
    // 创建一个 ref 来存储 textInput 的 DOM 元素
    this.textInput = React.createRef();
    this.focusTextInput = this.focusTextInput.bind(this);
  }

  focusTextInput() {
    // 直接使用原生 API 使 text 输入框获得焦点
    // 注意：我们通过 "current" 来访问 DOM 节点
    this.textInput.current.focus();
    console.log(this.textInput.current);//这边输出input的dom对象
  }

  render() {
    // 告诉 React 我们想把 <input> ref 关联到
    // 构造器里创建的 `textInput` 上
    return (
      <div>
        <input
          type="text"
          ref={this.textInput} />

        <input
          type="button"
          value="点击我获取焦点"
          onClick={this.focusTextInput}
        />
      </div>
    );
   }
 }
```

### 9.5 使用 React.forwardRef 传递ref

> 使用 `React.forwardRef` 来获取传递 ref，React 传递 ref 给 fowardRef 内函数 (props, ref) => ...，作为其第二个参数，然后转发到它渲染的 DOM

```react
const Input = React.forwardRef((props, ref) => (
    <input ref={ref}/>
));

class App extends React.Component {
    constructor (props) {
        super(props);
        this.inputELe = React.createRef();
        this.handleClick = this.handleClick.bind(this)
    }
    handleClick () {
        this.inputEle.current.focus();
        console.log(this.inputEle.current.value)
    }
    render () {
        return (
        	<div>
            	<Input ref={this.inputEle}></Input>
                <botton onClick={this.handleClick}>点击</botton>
            </div>
        )
    }
}


```



## 10. Context 上下文

Context 通过组件树提供了一个共享数据的方法，避免了组件深层嵌套级手动的传递 props 属性

### 10.1 Context 基本语法

```react
// 用React.createContext 创建一个上下文的容器，defaultValue 可以设置共享的默认数据
const { Provider, Consumer} = React.createContext(defaultValue);

// Provider: 生产者，定义（生产）共享数据的方法
<Provider value="data">
    {/* 这里面可以写需要渲染的jsx*/}
</Provider>

// Consumer: 消费者，使用 Provider 定义的数据 
<Consumer>
	{ 
        data => {
            return ( {/* 这里的data就是定义的数据，这里可以写渲染的jsx */})
        }
    }
</Consumer>
```



```react
import React from 'react'
const {Provider, Consumer} = React.createContext({});
// 父组件
function Parcomp (){
    store = { name: 'react', value: 'context'}
	return (
    	<Provider value={store}>
            <ChildComp></ChildComp>
        </Provider>
    )
}
function ChildComp () {
    return (
    	<Consumer>
        	{
                store => <div>name: {store.name}   value: {store.name}</div>
            }
        </Consumer>
    )
}
```

### 10.2  第二种形式：文件引入

```react
import 'React' from 'react'
const store = { name: '默认值', value: '默认值'}
export const {Provider, Consumer} = React.createContext(store)
```

```react
import 'React' from 'react'
import {Provider} from './ctx'
function Parcomp (){
    store = { name: 'react', value: 'context'}
	return (
    	<Provider value={store}>
            <ChildComp></ChildComp>
        </Provider>
    )
}
```

```react
import {Consumer} from './ctx'
function ChildComp () {
    return (
    	<Consumer>
        	{
                store => <div>name: {store.name}   value: {store.name}</div>
            }
        </Consumer>
    )
}
```

### 10.3 使用多个Context

```react
cont ctxVue = createContext({name:'默认'});
cont ctxReact = createContext({name:'默认'});

function ParComp () {
	return (
		<ctxVue.Provider value={{name: 'vue'}}>
        	<ctxReact.Provider value={{name: 'react'}}>
        		<ChildComp/>
        	</ctxReact.Provider>
        </ctxVue.Provider>
	)
}
function ChildComp () {
    return (
    	<ctxVue.Consumer>
          {vue => (
            <ctxReact.Consumer>
              {react => (
                <div>vue:{vue.name} , react: {react.name}</div>
              )}
            </ctxReact.Consumer>
          )}
        </ctxVue.Consumer>
    )
}

```

### 10.4  contextType

contextType 可以简化context ，不使用用consumer也可以共享变量

```react

import React, { Component } from 'react'
// 这里使用默认值 Provider 也省略了
// 如果使用了Provider 那么contextType 就会使用包裹着组件的最近的 Provider 定义的值
const ctx = React.createContext({name: 'vue'});

class CtxType extends Component {
    store = {name: 'react'}
    render() {
        return (
            <div>
               {this.context.name}
            </div>
        )
    }
}
CtxType.contextType = ctx;

export default CtxType;
```

### 11.5 更新Context

在一个数组树嵌套很深的组件中，更新 context 是很有必要的，我们可以通过给 context 传递一个函数，使consumer 跟新 context

```react
import {ThemeContext, themes} from './theme-context';
import ThemeTogglerButton from './theme-toggler-button';

class App extends React.Component {
  constructor(props) {
    super(props);

    this.toggleTheme = () => {
      this.setState(state => ({
        theme:
          state.theme === themes.dark
            ? themes.light
            : themes.dark,
      }));
    };

    // State 也包含了更新函数，因此它会被传递进 context provider。
    this.state = {
      theme: themes.light,
      toggleTheme: this.toggleTheme,
    };
  }

  render() {
    // 整个 state 都被传递进 provider
    return (
      <ThemeContext.Provider value={this.state}>
        <Content />
      </ThemeContext.Provider>
    );
  }
}

function Content() {
  return (
    <div>
      <ThemeTogglerButton />
    </div>
  );
}

ReactDOM.render(<App />, document.root);
```





##  11. 高阶组件

> 高阶组件就是参数为组件,返回值为新组件的函数.
>
> 高阶组件可分为两大类: 1. 代理方式的高阶组件  2. 继承方式的高阶组件
>
> 作用: 用于增强现有组件的功能

**高阶组件的意义**

1. **重用代码**: 有时候很多组件都要实现同一个逻辑, 此时把这部分逻辑提取出来,利用高阶组件的方式应用出去就可以减少很多组件重复的代码
2. **修改现有React组件的行为:** 有一个现成组件(例如: 第三方的), 我们想要添加一些功能, 但是我们不想去修改其内部的逻辑, 这个时候可以使用高阶组件, 通过现成的组件产生一个新组件,对现成的组件没有任何侵害

### 11.1 代理方式(属性代理)

> 因为属性代理类型的高阶组件返回的是一个标准的 React.Component 组件, 所以在 React 标准组件中能做什么, 那么在属性代理类型的告诫组件中就能做什么

- 操作 props
- 抽离 state (状态提升)
- 通过 ref 访问到组件
- 用其他元素包裹传入的组件

```react
// 无状态的 返回函数组件
function NewComp ( OldComp ) {
    return props => <OldComp {...props} />
}
// 有状态的
function NewComp ( OldComp ) {
    return class extends React.Component{
        //  此处可添加生命周期 
        render () {
            return <OldComp {... this.props} />
        }
    }
}
```

**操作 props**

```react
// 1. 操作props
function NewComp ( OldComp ) {
    return class extends React.Component{ 
        render () {
        	// 1.操作props
            const newProps = {
                name: 'react',
                id: '1'
            }
            return <OldComp {... this.props} {...newProps} />
        }
    }
}
```

**利用 props 提升 state**

```react
function NewComp(OldComp) {
    return class extends React.Component {
        constructor(props) {
            super(props);
            this.state = {
                name: '',
            };
        }
 
        onChange = () => {
            this.setState({
                name: 'react',
            });
        }
 
        render() {
            const newProps = {
                name: {
                    value: this.state.name,
                    onChange: this.onChange,
                },
            };
            return <OldComp {...this.props} {...newProps} />;
        }
    };
 
}

// 使用

const NameInput = props => (<input name="name" {...props.name} />);
export default withOnChange(NameInput);

```

**通过 ref 访问到组件的实例  (返回函数组件不可用)**

```react
import React from 'react'
// 现有组件
class SayHello extends React.Component {
	sayHello = () => {
		alert('hello')
	}
	render () {
		return (<div>我是SayHello组件</div>)
	}
}
// 高阶组件
function NewComp (OldComp) {
	return class extends React.Component{
		render() {
			return (<OldComp {...this.props} ref={el => el.sayHello()} />)
		}
	}
}
// 使用
export default NewComp(SayHello)

```

**用其他元素包裹传入的组件**

```
// 为旧的组件添加背景
function NewComp ( OldComp ) {
	return class extends React.Component {
		render () {
			return (
				<div style={{background: '#ccc'}}>
					<OldComp {...props} />
				</div>
			)
		}
	}
}
```

### 11.2 继承方式(反向继承)

> 继承方式其实就是一个接收一个组件作为参数传入, 但是返回的是一个继承了传入组件的类, 并且在该类的render() 方法中返回 super.render() 方法

```react
// 最简单的继承方式
function NewComp (OldComp) {
	return class extends OldComp{
        render () {
            return super.render();
        }
    }
}
```

- 操作state(不建议这么做)
- 渲染劫持

**操作 state**

```react
// 下面打印出了组件的state 和 props
// 还可以修改 state, 但是不建议这么做,因为会导致state难以维护
function NewComp (OldComp) {
	return class extends OldComp{
		render(){
			return (
				<div>
					<p>props: {JSON.stringify(this.state)}</p>
					<p>props: {JSON.stringify(this.props)}</p>
					{super.render()}
				</div>
			)
		}
	}
}
```

**渲染劫持**

```react
// 我们可以有条件的展示元素
// 通过props.isLoading 这个条件来判断渲染哪个组
function withLoading(WrappedComponent) {
    return class extends WrappedComponent {
        render() {
            if(this.props.isLoading) {
                return <Loading />;
            } else {
                return super.render();
            }
        }
    };
}

```

### 11.3 高阶组件存在的问题

1. 静态方法丢失: 因为原始组件被包裹于一个容器组件内，也就意味着新组件会没有原始组件的任何静态方法
2. refs 不能透传: 新组件上的ref是指向新组件的实例, 而不是原来的组件的实例
3. 反向继承不能保证完整的子组件树被解析

## 12 React 顶层API

### 12.1 React.Component

React.Component 是使用 ES6 classes 方式定义 React 组件的基类

```react
class Comp extends React.Component{
	render () {
		return <div>hello, {this.props.name}</div>
	}
}
```

### 12.2 React.PureComponent 

与 React.Component 相似, 区别在于`React.PureComponent` 中以浅层对比 prop 和 state 的方式来实现了shouldComponentUpdate()

> <font color=red>`React.PureComponent` 中的 `shouldComponentUpdate()` 仅作对象的浅层比较。如果对象中包含复杂的数据结构，则有可能因为无法检查深层的差别，产生错误的比对结果。仅在你的 props 和 state 较为简单时，才使用 `React.PureComponent`，或者在深层数据结构发生变化时调用 [`forceUpdate()`](https://react.docschina.org/docs/react-component.html#forceupdate) 来确保组件被正确地更新。</font>

### 12.3 React.memo

React.memo 作为高阶组件. 他与 React.PureComponent 非常相似,但只适用于函数组件.

`React.memo` 仅检查 props 变更。如果函数组件被 `React.memo` 包裹，且其实现中拥有 useState 或 userConext 的 Hook，当 context 发生变化时，它仍会重新渲染

```react
const MyComponent = React.memo(function MyComponent(props) {
	// 用 props 渲染
	return <div>hello, {props.name}</div>
});
```

默认情况下其只会对复杂对象做浅层对比，如果你想要控制对比过程，那么请将自定义的比较函数通过第二个参数传入来实现。

```
function MyComponent(props) {
  /* 使用 props 渲染 */
}
function areEqual(prevProps, nextProps) {
  /*
  如果prevProps 和 nextProps结果一致则返回 true， 否则返回 false
  和 shouldComponentUpdate 相反
  */
}
export default React.memo(MyComponent, areEqual);
```

### 12.4 React.Children

**React.Children 提供了用于处理 this.props.children 不透明数据结构的实用方法**

> 不透明意思就是不确定, props.children 可以是任何类型,比如数组,函数,对象等等, 如果有人将一个函数作为Child传过来 this.props.children.map 就会报错, 而React.Children.map就不会报错

```react
/*在 children 里的每个直接子节点上调用一个函数，并将 this 设置为 thisArg。如果 children 是一个数组，它将被遍历并为数组中的每个子节点调用该函数。如果子节点为 null 或是 undefined，则此方法将返回 null 或是 undefined，而不会返回数组*/
React.Children.map(children, function[(thisArg)])

//与 React.Children.map() 类似，但它不会返回一个数组。
React.Children.forEach(children, function[(thisArg)])

//返回 children 中的组件总数量，等同于通过 map 或 forEach 调用回调函数的次数。
React.Children.count(children)

//验证 children 是否只有一个子节点（一个 React 元素），如果有则返回它，否则此方法会抛出错误。
React.Children.only(children)

//将 children 这个复杂的数据结构以数组的方式扁平展开并返回，并为每个子节点分配一个 key
React.Children.toArray(children)

//React.Fragment 组件能够在不额外创建 DOM 元素的情况下，让 render() 方法中返回多个元素
render() {
  return (
    <React.Fragment>
      Some text.
      <h2>A heading</h2>
    </React.Fragment>
  );
}

```

```
class Comp extends React.Component {
  render() {
    const children = this.props.children
    return (
      <div>
        {React.Children.map(children, (child, i) => {
          if (i < 1) return
          return child
        })}
      </div>
    )
  }
}
<Comp>
	<p>1</p>
	<p>2</p>
	<p>3</p>
</Comp>
```

### 12.5 React.cloneElement

```
React.cloneElement( element, [props], [...children])
```

以 ==element== 为模板克隆并返回新的React元素，新的react元素的==props== 将是参数的props和原始的props浅合并后的结果，children将取代原始的子元素，原始的key和ref将会被保留

可以用于向子组件传递值

```react
import React, { Component } from 'react';
// 父元素
class parComp extends Component {
    constructor(props) {
        super(props)
        this.state = {
            count: 1
        }
        this.handleClick = this.handleClick.bind(this);
    }

    handleClick() {
        const num = this.state.count + 1
        this.setState({
            count: num
        })
    }

    render() {
        const childrenWithProps = React.Children.map(
        	this.props.children, 
        	child => React.cloneElement(child, 
                {
                    parentState: this.state.count,
                    handleClick: this.handleClick
                }
        ));
        return (
            <div style={{border:"1px solid blue"}}>
                <span>父容器:</span>
                { childrenWithProps }
            </div>
        )
    }
}
// 子元素
export class MySub extends Component {
    constructor(props) {
        super(props)
        this.state = {
            flag: false
        }
    }

    render() {
        return (
            <div style={{margin: "15px", border: "1px solid red"}}>
                子元素:{this.props.subInfo}
                <br/>
                父组件属性count值: { this.props.parentState }
                <br/>
                <button onClick={ () => this.props.handleClick() } >click me</button>
            </div>
        )
    }
}

ReactDOM.render (
    (
        <MyContainer>
            <MySub subInfo={"1"}/>
            <MySub subInfo={"2"}/>
        </MyContainer>
    )
    , document.getElementById('root'))
```





### 12.6 其他Api

```
React.createRef 创建一个能够通过 ref 属性附加到 React 元素的 ref。
this.inputRef = React.createRef();

React.forwardRef 会创建一个React组件，这个组件能够将其接受的 ref 属性转发到其组件树下的另一个组件中
React.forwardRef

React.lazy() 允许你定义一个动态加载的组件。这有助于缩减 bundle 的体积，并延迟加载在初次渲染时未用到的组件
const SomeComponent = React.lazy(() => import('./SomeComponent'));

React.Suspense 可以指定加载指示器（loading indicator），以防其组件树中的某些子组件尚未具备渲染条件。目前，懒加载组件是 <React.Suspense> 支持的唯一用例
// 该组件是动态加载的
const OtherComponent = React.lazy(() => import('./OtherComponent'));

function MyComponent() {
  return (
    // 显示 <Spinner> 组件直至 OtherComponent 加载完成
    <React.Suspense fallback={<Spinner />}>
      <div>
        <OtherComponent />
      </div>
    </React.Suspense>
  );
}
```






## 13. HOOK

> 可以在函数组件中使用 react 的 特性 如  state

### 13.1 useState

```react
// 引入state Hook
import React, { useState } from 'react';
function Example () {
    // 声明state变量 count, 默认值是0
    const [count, setCount] = useState(0)
    // 定义多个state
    const [name, setName] = useState('react')
    const [todos, setTodos] userState([{text:'学习Hook'}])
    
    // 使用
    function handleClick () {
        setCount(1); // 更改count的值，就等于class写法中的 setState()
    }
    return (
    	<div>
        	<p>name:{ name }</p>
            <button onClick={() => setName('Hook')}></button>
        </div>
    )
    
}
```

### 13.2  useEffect

> *Effect Hook* 可以让你在函数组件中执行副作用操作

```react
// 简单的小例子
import React, {useState, useEffect} from 'react';

function Example () {
	const [count, setCount] = useState(0)
    
    useEffect(() => {
        // 副作用 ajax请求，订阅  等等
        document.title = `你点击了${count}次`
    })
    return (
    	<div>
        	<p>你点击了{count}次</p>
            <button onClick={() => setCount(count+1)}></button>
        </div>
    )
}
```

```
// useEffect 的第二个参数
useEffect(() => {
  document.title = `You clicked ${count} times`;
}, [count]);
// 不传：默认是每次都执行
// 传递一个 [](空数组)：effect 只执行一次
// 非空数组：当数组中的内容发生变化时执行
```

### 13.3 useContext

接收一个 context 对象（`React.createContext` 的返回值）并返回该 context 的当前值。当前的 context 值由上层组件中距离当前组件最近的 value决定。

```react
const ctx = React.createContext()

function App () {
	return (
    	<ctx.Provider value={{name: 'react'}}>
        	<Foo></Foo>
        </ctx.Provider>
    )
}
function Foo() {
    let store = useContext(ctx);
    return (
    	<div>name: {store.name}</div>
    )
}
```

### 13.4 自定义HOOK

**定义 Hook 是一个函数，其名称以 “`use`” 开头，函数内部可以调用其他的 Hook**

```react
// 这是一个修改页面标题的HOOK
useSetTitle (title) {
	useEffect(()=> {
		document.title= 'title'
	},[title])
}
```













