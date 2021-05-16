---
layout: post
title: "React学习笔记"
date: 2021-05-15 20:16:06 +0800
comments: true
categories: 
---
### 入门教程
- 状态提升：当你遇到需要同时获取多个子组件数据，或者两个组件之间需要相互通讯的情况时，需要把子组件的 state 数据提升至其共同的父组件当中保存。之后父组件可以通过 props 将状态数据传递到子组件当中。这样应用当中所有组件的状态数据就能够更方便地同步共享了。

- 为了提高可读性，我们把返回的 React 元素拆分成了多行，同时在最外层加了小括号，这样 JavaScript 解析的时候就不会在 return 的后面自动插入一个分号从而破坏代码结构了。

- 在 React 中，有一个命名规范，通常会将代表事件的监听 prop 命名为 on[Event]，将处理事件的监听方法命名为 handle[Event] 这样的格式。
 
- concat() 方法可能与你比较熟悉的 push() 方法不太一样，它并不会改变原数组，所以我们推荐使用 concat()
 
### KEY
- 每当一个列表重新渲染时，React 会根据每一项列表元素的 key 来检索上一次渲染时与每个 key 所匹配的列表项。如果 React 发现当前的列表有一个之前不存在的 key，那么就会创建出一个新的组件。如果 React 发现和之前对比少了一个 key，那么就会销毁之前对应的组件。如果一个组件的 key 发生了变化，这个组件会被销毁，然后使用新的 state 重新创建一份。

- key 是 React 中一个特殊的保留属性（还有一个是 ref，拥有更高级的特性）。当 React 元素被创建出来的时候，React 会提取出 key 属性，然后把 key 直接存储在返回的元素上。虽然 key 看起来好像是 props 中的一个，但是你不能通过 this.props.key 来获取 key。React 会通过 key 来自动判断哪些组件需要更新。组件是不能访问到它的 key 的。

- 我们强烈推荐，每次只要你构建动态列表的时候，都要指定一个合适的 key。如果你没有找到一个合适的 key，那么你就需要考虑重新整理你的数据结构了，这样才能有合适的 key。

### 函数组件
- 如果你想写的组件只包含一个 render 方法，并且不包含 state，那么使用函数组件就会更简单。我们不需要定义一个继承于 React.Component 的类，我们可以定义一个函数，这个函数接收 props 作为参数，然后返回需要渲染的元素。函数组件写起来并不像 class 组件那么繁琐，很多组件都可以使用函数组件来写。
 
定义组件最简单的方式就是编写 JavaScript 函数：
 
```
 function Welcome(props) {
	return <h1>Hello, {props.name}</h1>;
}
￼ES6：
class Welcome extends React.Component {
	render() {
		return <h1>Hello, {this.props.name}</h1>;
	}
}
注意： 组件名称必须以大写字母开头。
```
 
### Props的只读性

```
 function sum(a, b) {
	return a + b;
}
```
 
- 这样的函数被称为“纯函数”，因为该函数不会尝试更改入参，且多次调用下相同的入参始终返回相同的结果。
相反，下面这个函数则不是纯函数，因为它更改了自己的入参：

```
function withdraw(account, amount) {
	account.total -= amount;
 }
```

- React 非常灵活，但它也有一个严格的规则：
所有 React 组件都必须像纯函数一样保护它们的 props 不被更改。
 
### 正确地使用 State

- 不要直接修改 State，要使用setState（）
- State 的更新可能是异步的
 
出于性能考虑，React 可能会把多个 setState() 调用合并成一个调用。
因为 this.props 和 this.state 可能会异步更新，所以你不要依赖他们的值来更新下一个状态。

```
 // Wrong
this.setState({
	counter: this.state.counter + this.props.increment,
});
```
要解决这个问题，可以让 setState() 接收一个函数而不是一个对象。这个函数用上一个 state 作为第一个参数，将此次更新被应用时的 props 做为第二个参数：
 
```
 // Correct
this.setState((state, props) => ({
	counter: state.counter + props.increment
}));
this.setState(state => ({
	isToggleOn: !state.isToggleOn
}));
```
 
- State 的更新会被合并

 当你调用 setState() 的时候，React 会把你提供的对象合并到当前的 state。
 
- 这通常会被叫做“自上而下”或是“单向”的数据流。任何的 state 总是所属于特定的组件，而且从该 state 派生的任何数据或 UI 只能影响树中“低于”它们的组件。

### 事件处理
 
```
 class LoggingButton extends React.Component {
  handleClick() {
    console.log('this is:', this);
  }

  render() {
    // 此语法确保 `handleClick` 内的 `this` 已被绑定。
    return (
      <button onClick={() => this.handleClick()}>
        Click me
      </button>
    );
  }
}
```

- 此语法问题在于每次渲染 LoggingButton 时都会创建不同的回调函数。在大多数情况下，这没什么问题，但如果该回调函数作为 prop 传入子组件时，这些组件可能会进行额外的重新渲染。我们通常建议在构造器中绑定或使用 class fields 语法来避免这类性能问题。
 
```
 class LoggingButton extends React.Component {
  // 此语法确保 `handleClick` 内的 `this` 已被绑定。
  // 注意: 这是 *实验性* 语法。
  handleClick = () => {
  //  class fields 
    console.log('this is:', this);
  }

  render() {
    return (
      <button onClick={this.handleClick}>
        Click me
      </button>
    );
  }
}
```
 
```
 <button onClick={(e) => this.deleteRow(id, e)}>Delete Row</button>
<button onClick={this.deleteRow.bind(this, id)}>Delete Row</button>
```
 
- 在这两种情况下，React 的事件对象 e 会被作为第二个参数传递。如果通过箭头函数的方式，事件对象必须显式的进行传递，而通过 bind 的方式，事件对象以及更多的参数将会被隐式的进行传递。
 
### 列表 & Key
- 一个元素的 key 最好是这个元素在列表中拥有的一个独一无二的字符串
 - 一个好的经验法则是：在 map() 方法中的元素需要设置 key 属性。
 
### React.lazy
- React.lazy 函数能让你像渲染常规组件一样处理动态引入（的组件）。
 
```
 使用之前：
import OtherComponent from './OtherComponent';
使用之后：
const OtherComponent = React.lazy(() => import('./OtherComponent'));
```
 
- React.lazy 目前只支持默认导出（default exports）。如果你想被引入的模块使用命名导出（named exports），你可以创建一个中间模块，来重新导出为默认模块。这能保证 tree shaking 不会出错，并且不必引入不需要的组件。
 
```
 // ManyComponents.js
export const MyComponent = /* ... */;
export const MyUnusedComponent = /* ... */;
// MyComponent.js
export { MyComponent as default } from "./ManyComponents.js";
// MyApp.js
import React, { lazy } from 'react';
const MyComponent = lazy(() => import("./MyComponent.js"));
```
 
### Context
- Context 主要应用场景在于很多不同层级的组件需要访问同样一些的数据。请谨慎使用，因为这会使得组件的复用性变差。
- Context 设计目的是为了共享那些对于一个组件树而言是“全局”的数据，例如当前认证的用户、主题或首选语言。
 
```
 const MyContext = React.createContext(defaultValue);
```

- 创建一个 Context 对象。当 React 渲染一个订阅了这个 Context 对象的组件，这个组件会从组件树中离自身最近的那个匹配的 Provider 中读取到当前的 context 值。
只有当组件所处的树中没有匹配到 Provider 时，其 defaultValue 参数才会生效。这有助于在不使用 Provider 包装组件的情况下对组件进行测试。注意：将 undefined 传递给 Provider 的 value 时，消费组件的 defaultValue 不会生效。

```
<MyContext.Provider value={/* 某个值 */}>
￼MyClass.contextType = MyContext;
```

- 挂载在 class 上的 contextType 属性会被重赋值为一个由 React.createContext() 创建的 Context 对象。这能让你使用 this.context 来消费最近 Context 上的那个值。你可以在任何生命周期中访问到它，包括 render 函数中。
 
```
 class MyClass extends React.Component {
static contextType = MyContext;
render() {
let value = this.context;
/* 基于这个值进行渲染工作 */
}
}
￼<MyContext.Consumer>
￼{value => /* 基于 context 值进行渲染*/}
￼</MyContext.Consumer>
```
 
```
 const MyContext = React.createContext(/* some value */);
MyContext.displayName = 'MyDisplayName';
<MyContext.Provider> // "MyDisplayName.Provider" 在 DevTools 中
<MyContext.Consumer> // "MyDisplayName.Consumer" 在 DevTools 中
```
 
### 错误边界
 
- 错误边界是一种 React 组件，这种组件可以捕获并打印发生在其子组件树任何位置的 JavaScript 错误，并且，它会渲染出备用 UI
 - 错误边界无法捕获以下场景中产生的错误：
  1. 事件处理
  2. 异步代码（例如 setTimeout 或 requestAnimationFrame 回调函数
  3. 服务端渲染
  4. 它自身抛出来的错误（并非它的子组件）
  
- 如果一个 class 组件中定义了 static getDerivedStateFromError() 或 componentDidCatch() 这两个生命周期方法中的任意一个（或两个）时，那么它就变成一个错误边界。当抛出错误后，请使用 static getDerivedStateFromError() 渲染备用 UI ，使用 componentDidCatch() 打印错误信息。

```
   static getDerivedStateFromError(error) {
    // 更新 state 使下一次渲染能够显示降级后的 UI
    return { hasError: true };
  }

  componentDidCatch(error, errorInfo) {
    // 你同样可以将错误日志上报给服务器
    logErrorToMyService(error, errorInfo);
  }
```
 
### Refs 转发
- Ref 转发是一个可选特性，其允许某些组件接收 ref，并将其向下传递（换句话说，“转发”它）给子组件。

```
//3.React 传递 ref 给 forwardRef 内函数 (props, ref) => ...，作为其第二个参数。
const FancyButton = React.forwardRef((props, ref) => (
//4.我们向下转发该 ref 参数到 <button ref={ref}>，将其指定为 JSX 属性。
//5.当 ref 挂载完成，ref.current 将指向 <button> DOM 节点。
  <button ref={ref} className="FancyButton">
    {props.children}
  </button>
));

// 1.我们通过调用 React.createRef 创建了一个 React ref 并将其赋值给 ref 变量。
const ref = React.createRef();
// 2.我们通过指定 ref 为 JSX 属性，将其向下传递给 <FancyButton ref={ref}>。
<FancyButton ref={ref}>Click me!</FancyButton>;
```

- 第二个参数 ref 只在使用 React.forwardRef 定义组件时存在。常规函数和 class 组件不接收 ref 参数，且 props 中也不存在 ref。

- 一个输出组件 props 到控制台的 HOC (高阶组件)示例:

```
function logProps(WrappedComponent) {
  class LogProps extends React.Component {
    componentDidUpdate(prevProps) {
      console.log('old props:', prevProps);
      console.log('new props:', this.props);
    }

    render() {
      return <WrappedComponent {...this.props} />;
    }
  }

  return LogProps;
}

// 使用
class FancyButton extends React.Component {
  focus() {
    // ...
  }

  // ...
}

// 我们导出 LogProps，而不是 FancyButton。
// 虽然它也会渲染一个 FancyButton。
export default logProps(FancyButton);
```

- 以上例子直接使用ref会挂载到logProps上，而不是FancyButton

- 改进示例：

```
function logProps(Component) {
  class LogProps extends React.Component {
    componentDidUpdate(prevProps) {
      console.log('old props:', prevProps);
      console.log('new props:', this.props);
    }

    render() {
      const {forwardedRef, ...rest} = this.props;

      // 将自定义的 prop 属性 “forwardedRef” 定义为 ref
      return <Component ref={forwardedRef} {...rest} />;
    }
  }

  // 注意 React.forwardRef 回调的第二个参数 “ref”。
  // 我们可以将其作为常规 prop 属性传递给 LogProps，例如 “forwardedRef”
  // 然后它就可以被挂载到被 LogProps 包裹的子组件上。
  return React.forwardRef((props, ref) => {
    return <LogProps {...props} forwardedRef={ref} />;
  });
}
```

### Fragments
- React 中的一个常见模式是一个组件返回多个元素。Fragments 允许你将子列表分组，而无需向 DOM 添加额外节点。

```
render() {
  return (
    <React.Fragment>
      <ChildA />
      <ChildB />
      <ChildC />
    </React.Fragment>
  );
}
```

- 你可以使用一种新的，且更简短的语法来声明 Fragments。它看起来像空标签：

```
class Columns extends React.Component {
  render() {
    return (
      <>
        <td>Hello</td>
        <td>World</td>
      </>
    );
  }
}
```

- key 是唯一可以传递给 Fragment 的属性。

### 高阶组件HOC
- 高阶组件是参数为组件，返回值为新组件的函数

- 组件是将 props 转换为 UI，而高阶组件是将组件转换为另一个组件。

- HOC 不会修改传入的组件，也不会使用继承来复制其行为。相反，HOC 通过将组件包装在容器组件中来组成新组件。HOC 是纯函数，没有副作用。

#### 将不相关的 props 传递给被包裹的组件
- HOC 为组件添加特性。自身不应该大幅改变约定。HOC 返回的组件与原组件应保持类似的接口。

HOC 应该透传与自身无关的 props。大多数 HOC 都应该包含一个类似于下面的 render 方法：

```
render() {
  // 过滤掉非此 HOC 额外的 props，且不要进行透传
  const { extraProp, ...passThroughProps } = this.props;

  // 将 props 注入到被包装的组件中。
  // 通常为 state 的值或者实例方法。
  const injectedProp = someStateOrInstanceMethod;

  // 将 props 传递给被包装组件
  return (
    <WrappedComponent
      injectedProp={injectedProp}
      {...passThroughProps}
    />
  );
}
```

#### 最大化可组合性

#### 包装显示名称以便轻松调试
- 请选择一个显示名称，以表明它是 HOC 的产物。
- 最常见的方式是用 HOC 包住被包装组件的显示名称。比如高阶组件名为 withSubscription，并且被包装组件的显示名称为 CommentList，显示名称应该为 WithSubscription(CommentList)：

```
function withSubscription(WrappedComponent) {
  class WithSubscription extends React.Component {/* ... */}
  WithSubscription.displayName = `WithSubscription(${getDisplayName(WrappedComponent)})`;
  return WithSubscription;
}

function getDisplayName(WrappedComponent) {
  return WrappedComponent.displayName || WrappedComponent.name || 'Component';
}
```

#### HOC注意事项
- 不要在 render 方法中使用 HOC

```
render() {
  // 每次调用 render 函数都会创建一个新的 EnhancedComponent
  // EnhancedComponent1 !== EnhancedComponent2
  const EnhancedComponent = enhance(MyComponent);
  // 这将导致子树每次渲染都会进行卸载，和重新挂载的操作！
  return <EnhancedComponent />;
}
```
这不仅仅是性能问题 - 重新挂载组件会导致该组件及其所有子组件的状态丢失。
你可以在组件的生命周期方法或其构造函数中进行调用。

- 务必复制静态方法
当你将 HOC 应用于组件时，原始组件将使用容器组件进行包装。这意味着新组件没有原始组件的任何静态方法。

```
// 定义静态函数
WrappedComponent.staticMethod = function() {/*...*/}
// 现在使用 HOC
const EnhancedComponent = enhance(WrappedComponent);

// 增强组件没有 staticMethod
typeof EnhancedComponent.staticMethod === 'undefined' // true
```
为了解决这个问题，你可以在返回之前把这些方法拷贝到容器组件上：

```
function enhance(WrappedComponent) {
  class Enhance extends React.Component {/*...*/}
  // 必须准确知道应该拷贝哪些方法 :(
  Enhance.staticMethod = WrappedComponent.staticMethod;
  return Enhance;
}
```

另一个可行的方案是再额外导出这个静态方法。

```
// 使用这种方式代替...
MyComponent.someFunction = someFunction;
export default MyComponent;

// ...单独导出该方法...
export { someFunction };

// ...并在要使用的组件中，import 它们
import MyComponent, { someFunction } from './MyComponent.js';

```

- Refs 不会被传递




 