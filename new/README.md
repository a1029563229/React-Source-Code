# 新知识点（个人）

```ts
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
```
