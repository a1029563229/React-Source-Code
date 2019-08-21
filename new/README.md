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
```
