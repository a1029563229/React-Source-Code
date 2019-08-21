# 新知识点（个人）

```tsx
// 显式获取类名
const constructor = publicInstance.constructor;
const componentName =
  (constructor && (constructor.displayName || constructor.name)) ||
  "ReactClass";

class User {
  constructor() {}
}
const user = new User();
console.log(user.constructor.name); // User

// component.forceUpdate(callback);
// 默认情况下，当组件的 state 或 props 发生变化时，组件将重新渲染。
// 如果 render() 方法依赖于其他数据，则可以调用 forceUpdate() 强制让组件重新渲染。
// 调用 forceUpdate() 以致使组件调用 render() 方法，此操作会跳过该组件的 shouldComponentUpdate()。
// 但其子组件会触发正常的生命周期方法，包括 shouldComponentUpdate() 方法。如果标记发生变化，React 仍将只更新 DOM。
// 通常你应该避免使用 forceUpdate()，尽量在 render() 中使用 this.props 和 this.state。
Component.prototype.forceUpdate = function(callback) {
  this.updater.enqueueForceUpdate(this, callback, 'forceUpdate');
};

// 在 DEV 环境利用 getter 弹出警告信息
const defineDeprecationWarning = function(methodName, info) {
  Object.defineProperty(Component.prototype, methodName, {
    get: function() {
      lowPriorityWarning(
        false,
        '%s(...) is deprecated in plain JavaScript React classes. %s',
        info[0],
        info[1],
      );
      return undefined;
    },
  });
};

// 重命名导出 
// originalModuleName as newModuleName
export {
  forEachChildren as forEach,
  mapChildren as map,
  countChildren as count,
  onlyChild as only,
  toArray,
};

// React.Fragment
// React.Fragment 组件能够在不额外创建 DOM 元素的情况下，让 render() 方法中返回多个元素。
// 你也可以使用其简写语法 <></>
<React.Fragment></React.Fragment>

// 相当于 <type(div || span || ...) />
// 创建并返回指定类型的新 React 元素。
// 其中的类型参数既可以是标签名字符串（如 'div' 或 'span'），也可以是 React 组件类型（class 组件或函数组件），或是 React fragment 类型。
// 使用 JSX 编写的编码将会被转换为使用 React.createElement() 的形式。如果使用了 JSX 方式，那么一般来说就不需要直接调用 React.createElement().
// 相当于执行 render() 函数，最后执行的就是 React.createElement()
React.createElement(
  type, // component.type 字符串（如果是 function 则自动被转为了 type.displayName || type.name || 'Unknown'）
  [props],
  [...children]
)

// React.createElement 接受的 type 可以是字符串，也可以是一个类（函数）
// React.cloneElement 接受的 element 一定是一个实例，这样才能获取到实例的属性

// React.cloneElement()
// 以 element 元素为样板克隆并返回新的 React 元素。返回元素的 props 是将新的 props 与原始元素的 props 浅层合并后的结果。
// 新的子元素将取代现有的子元素，而来自原始元素的 key 和 ref 将被保留。
// React.cloneElement() 几乎等同于：
// <element.type {...element.props} {...props}>{children}</element.type>
// 但是，这也保留了组件的 ref。这意味着当通过 ref 获取子节点时，你将不会意外地从你祖先节点上窃取它。相同的 ref 将添加到克隆后的新元素中。
React.cloneElement(
  element, // 实例，ReactElement，由 React.createElement() 返回的对象
  [props],
  [...children]
)

// React.createContext
// 创建一个 Context 对象。当 React 渲染一个订阅了这个 Context 对象的组件，这个组件会从组件树中离自身最近的那个匹配的 Provider 中读取到当前的 context 值。
// 只有当组件所处的树中没有匹配到 Provider 时，其 defaultValue 参数才会生效。这有助于在不使用 Provider 包装组件的情况下对组件进行测试。
// 其内部实现就是创建了一个 context 树，树上有 Provider 和 Consumer，Consumer 又指向 context，从而形成一个闭环，可以达到访问同一个作用域中的 context 的效果。
// 在渲染节点时应该还有一些处理，比如将 Provider 中的 value 值赋值给 context，而 Consumer 接收到值后进行更新
const MyContext = React.createContext(defaultValue);

// Context.Provider
// 每个 Context 对象都会返回一个 Provider React 组件，它允许消费组件订阅 context 的变化。
// Provider 接收一个 value 属性，传递给消费组件。一个 Provider 可以和多个消费组件有对应关系。多个 Provider 也可以嵌套使用，里层的会覆盖外层的数据。
// 当 Provider 的 value 值发生变化时，它内部的所有消费组件都会重新渲染。Provider 及其内部 consumer 组件都不受制于 shouldComponentUpdate 函数，因此当 consumer 组件在其祖先组件退出更新的情况下也能更新。
// 通过新旧值检测来确定变化，使用了与 Object.is 相同的算法
// <MyContext.Provider value={/* 某个值 */}>

// class.contextType
// 挂载在 class 上的 contextType 属性会复制为一个由 React.createContext() 创建的 Context 对象。
// 这能让你使用 this.context 来消费最近 Context 的那个值。你可以在任何生命周期中访问到它，包括 render 函数中。
class MyClass extends React.Component {
  componentDidMount() {
    let value = this.context;
    /* 在组件挂载完成后，使用 MyContext 组件的值来执行一些有副作用的操作 */
  }
  componentDidUpdate() {
    let value = this.context;
    /* ... */
  }
  componentWillUnmount() {
    let value = this.context;
    /* ... */
  }
  render() {
    let value = this.context;
    /* 基于 MyContext 组件的值进行渲染 */
  }
}
MyClass.contextType = MyContext;

// Context.Consumer
// 这里，React 组件也可以订阅到 context 变更，这能让你在函数式组件中完成订阅 context。
<MyContext.Consumer>
  {value => /* 基于 context 值进行渲染*/}
</MyContext.Consumer>

// React.Suspense
// Suspense 使得组件可以等待“等待”某些操作结束后，再进行渲染。目前，Suspense 仅支持的使用场景是：通过 React.lazy 动态加载组件。
// 它将在未来支持其他使用场景，如数据获取等。
// React.Suspense 可以指定加载指示器（loading indicator），以防其组件树中的某些子组件尚未具备渲染条件。
// 目前，懒加载组件是 <React.Suspense> 支持的唯一用例：
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

// React.lazy
// React.lazy 和 Suspense 技术还不支持服务端渲染。如果你想要在使用服务端渲染的应用中使用，我们推荐 Loadable Components 这个库。
// React.lazy 函数能让你像渲染常规组件一样处理动态引入（的组件）。
const OtherComponent = React.lazy(() => import('./OtherComponent'));

function MyComponent() {
  return (
    <div>
      <OtherComponent />
    </div>
  );
}

// React.forwardRef
// React.forwardRef 会创建一个 React 组件，这个组件能够将其接受的 ref 属性转发到其组件树下的另一个组件中。在下面两种场景中特别有用：
// 转发 refs 到 DOM 组件 || 在高阶组件中转发 refs
const FancyButton = React.forwardRef((props, ref) => (
  <button ref={ref} className="FancyButton">
    {props.children}
  </button>
));

// You can now get a ref directly to the DOM button:
const ref = React.createRef();
<FancyButton ref={ref}>Click me!</FancyButton>;

// React.memo 为高阶组件。它与 React.PureComponent 非常相似，但它适用于函数组件，但不适用于 class 组件。
const MyComponent = React.memo(function MyComponent(props) {
  /* 使用 props 渲染 */
});

// React Hook
// Hook 是一些可以让你在函数组件里“钩入” React state 及生命周期等特性的函数。
// Hook 使你在非 class 的情况下可以使用更多的 React 特性。 从概念上讲，React 组件一直更像是函数。而 Hook 则拥抱了函数，同时也没有牺牲 React 的精神原则。Hook 提供了问题的解决方案，无需学习复杂的函数式或响应式编程技术。
// 我们准备让 Hook 覆盖所有 class 组件的使用场景，但是我们将继续为 class 组件提供支持。在 Facebook，我们有成千上万的组件用 class 书写，我们完全没有重写它们的计划。相反，我们开始在新的代码中同时使用 Hook 和 class。
// 通过使用 Hook，你可以把组件内相关的副作用组织在一起（例如创建订阅及取消订阅），而不要把它们拆分到不同的生命周期函数里。
function FriendStatusWithCounter(props) {
  const [count, setCount] = useState(0);
  useEffect(() => {
    document.title = `You clicked ${count} times`;
  });

  const [isOnline, setIsOnline] = useState(null);
  useEffect(() => {
    ChatAPI.subscribeToFriendStatus(props.friend.id, handleStatusChange);
    return () => {
      ChatAPI.unsubscribeFromFriendStatus(props.friend.id, handleStatusChange);
    };
  });

  function handleStatusChange(status) {
    setIsOnline(status.isOnline);
  }
  // ...

// Hook 使用规则
// Hook 就是 Javascript 函数，但是使用它们会有两个额外的规则：
// 只能在函数最外层调用 Hook。不要在循环、条件判断或子函数中调用。
// 只能在 React 的函数组件中调用 Hook。不要在其他 Javascript 函数中调用。

// Effect Hook 可以让你在函数组件中执行副作用操作
// 如果你熟悉 React class 的生命周期函数，你可以把 useEffect Hook 看做 componentDidMount，componentDidUpdate 和 componentWillUnmount 这三个函数的组合。
// useEffect 会在每次渲染后都执行吗？ 是的，默认情况下，它在第一次渲染之后和每次更新之后都会执行。
// 与 componentDidMount 或 componentDidUpdate 不同，使用 useEffect 调度的 effect 不会阻塞浏览器更新屏幕，这让你的应用看起来响应更快。
// 如果你已经习惯了使用 class，那么你可能会想知道为什么 effect 在每次重渲染时都会执行，而不是只在卸载组件的时候执行一次。让我们看一个实际的例子，看看为什么这个设计可以帮助我们创建 bug 更少的组件。
// 忘记正确地处理 componentDidUpdate 是 React 应用中常见的 bug 来源。
// useEffect 默认就会处理。它会在调用一个新的 effect 之前对前一个 effect 进行清理。
import React, { useState, useEffect } from 'react';

function Example() {
  const [count, setCount] = useState(0);

  // Similar to componentDidMount and componentDidUpdate:
  useEffect(() => {
    // Update the document title using the browser API
    document.title = `You clicked ${count} times`;
  });

  return (
    <div>
      <p>You clicked {count} times</p>
      <button onClick={() => setCount(count + 1)}>
        Click me
      </button>
    </div>
  );
}

// 通过跳过 Effect 进行性能优化
// 这是很常见的需求，所以它被内置到了 useEffect 的 Hook API 中。如果某些特定值在两次重渲染之间没有发生变化，你可以通知 React 跳过对 effect 的调用，只要传递数组作为 useEffect 的第二个可选参数即可：
// 如果想执行只运行一次的 effect（仅在组件挂载和卸载时执行），可以传递一个空数组（[]）作为第二个参数。这就告诉 React 你的 effect 不依赖于 props 或 state 中的任何值，所以它永远都不需要重复执行。这并不属于特殊情况 —— 它依然遵循依赖数组的工作方式。
useEffect(() => {
  document.title = `You clicked ${count} times`;
}, [count]); // 仅在 count 更改时更新

// useReducer
// useState 的替代方案。它接收一个形如 (state, action) => newState 的 reducer，并返回当前的 state 以及与其配套的 dispatch 方法。（如果你熟悉 Redux 的话，就已经知道它如何工作了。）
const [state, dispatch] = useReducer(reducer, initialArg, init);

// useCallback
// 把内联回调函数及依赖项数组作为参数传入 useCallback，它将返回该回调函数的 memoized 版本，该回调函数仅在某个依赖项改变时才会更新。当你把回调函数传递给经过优化的并使用引用相等性去避免非必要渲染（例如 shouldComponentUpdate）的子组件时，它将非常有用。
const memoizedCallback = useCallback(
  () => {
    doSomething(a, b);
  },
  [a, b],
);

// useMemo
// 把“创建”函数和依赖项数组作为参数传入 useMemo，它仅会在某个依赖项改变时才重新计算 memoized 值。这种优化有助于避免在每次渲染时都进行高开销的计算。
const memoizedValue = useMemo(() => computeExpensiveValue(a, b), [a, b]);

// useRef
// useRef 返回一个可变的 ref 对象，其 .current 属性被初始化为传入的参数（initialValue）。返回的 ref 对象在组件的整个生命周期内保持不变。
const refContainer = useRef(initialValue);
function TextInputWithFocusButton() {
  const inputEl = useRef(null);
  const onButtonClick = () => {
    // `current` 指向已挂载到 DOM 上的文本输入元素
    inputEl.current.focus();
  };
  return (
    <>
      <input ref={inputEl} type="text" />
      <button onClick={onButtonClick}>Focus the input</button>
    </>
  );
}

// useImperativeHandle
// useImperativeHandle 可以让你在使用 ref 时自定义暴露给父组件的实例值。在大多数情况下，应当避免使用 ref 这样的命令式代码。
useImperativeHandle(ref, createHandle, [deps])

// useLayoutEffect
// 其函数签名与 useEffect 相同，但它会在所有的 DOM 变更之后同步调用 effect。可以使用它来读取 DOM 布局并同步触发重渲染。在浏览器执行绘制之前，useLayoutEffect 内部的更新计划将被同步刷新。
// 尽可能使用标准的 useEffect 以避免阻塞视觉更新。
```