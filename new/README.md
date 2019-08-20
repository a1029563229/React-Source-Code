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
```
