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


```
