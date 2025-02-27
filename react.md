## 组件基础

### react事件机制

document监听所有事件：合成事件发生冒泡至document，react将事件内容封装并交由真正处理函数运行；

事件不是原生的浏览器事件，是react合成的事件，因此阻止冒泡调用event.preventDefault()而不是event.stopProppagation()

优点：减少内存消耗；组件挂载销毁统一订阅或移除

目的：

- **浏览器兼容**问题；是跨浏览器原生事件包装器，可跨浏览器开发；
- 有**事件池**管理，调用时复用，事件结束则销毁对象上的属性；不像传统的事件监听分配，造成高额内存分配；

与vue的区别：

vue使用原生事件，通过一些修饰符、自动兼容等方式解决兼容问题，但是并不像react一样完全屏蔽差异。

vue没有事件池管理，频繁触发的原生事件mouseover之类和自定义事件（组件间通信）的则会内存高

### react事件和普通HTML事件的区别

区别：

- 名称命名，原生全小写，react驼峰；
- 函数处理语法，原生字符串，react函数；
- react中，return false不能阻止浏览器默认事件，需用**preventDefault()**

合成事件对象优点：

- 兼容浏览器，跨平台；
- 事件统一存放至数组，避免频繁
- 方便统一管理和事务机制

### react组件事件代理及原理

基于虚拟dom实现合成事件层（SyntheticEvent）

事件处理器（定义的特定事件）接受合成事件对象的实例。

实例：符合W3C标准；与原生的浏览器事件拥有同样的接口；支持冒泡机制，所有的事件都自动绑定在最外层上；

原理：

- 事件委派：由于所有事件都绑在最外层，因此使用统一的事件监听器。事件监听器上维持了一个映射来保存所有组件内部事件监听和处理函数。
- 自动绑定：每个方法的上下文指向该组件的实例（自动绑定this为当前组件）

### React高阶组件、Render props、hooks的区别，为什么要不断迭代？

解决代码复用的方式

高阶组件（HOC）：基于 React 的组合特性而形成的设计模式。高阶组件是参数为组件，返回值为新组件的函数。

render props：使用prop共享代码；告知组件需要渲染什么内容的函数prop；

hook：帮助减少嵌套；

### JSX

需要双亲元素包裹原因：转为js对象，但不能在函数中返回多个对象。
