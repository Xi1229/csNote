## Vue基础

#### 基本原理

Object.defineProperty（vue3.0使用proxy ）将它们转为 getter/setter

#### 双向数据绑定的原理

vue2中，`Observer` 通过 `Object.defineProperty` 劫持每个数据属性的 `getter` 和 `setter`，当数据发生变化时（即 `setter` 被触发），`setter` 会通知 `Dep`，然后通过 `Dep` 通知notify相应的 `Watcher`。

数据劫持 和 发布者-订阅者模式

Object.defineProperty()劫持各属性setter，getter，在数据变动时发布消息给订阅者，触发相应的监听回调。

observer：劫持监听data、响应式数据和数组操作

compile：将模板解析为render函数，编译器为渲染函数创建了一个渲染 Watcher。Watcher 在执行渲染函数时，渲染函数中访问响应式数据时，会触发响应式数据的 `getter`。在 `getter` 中，Vue 会检查当前是否有活跃的 `Watcher`（即 `Dep.target` 是否不为 `null`）。如果存在，调用 `Dep.addDep()`，将当前 `Watcher` 添加到 `Dep` 的依赖列表中。`Watcher` 被记录在 `Dep` 中，成为该属性的依赖。当属性发生变化时，`Dep` 可以通知所有依赖（`Watcher`）更新。

dep：每个响应式属性都对应一个 `Dep` 实例。管理依赖，记录哪些 `Watcher` 依赖了某个数据属性，并在数据变化时通知这些 `Watcher` 进行更新

总结：observer监听劫持，setter被触发便通知dep，再notiy给watcher，再更新到视图。compile编译成渲染函数，创建watcher，访问数据触发getter，检查是否有活跃的watcher，添加到dep依赖。属性变化时，dep通知watcher更新

#### Object.defineProperty()数据劫持的缺点

有些操作无法拦截。vue3使用proxy解决这件事

#### MVVM、MVC、MVP的区别

MVC单向循环  观察者模式

MVVM ViewModel联系Model和View 双向数据绑定=》开发者只需要专注于数据的维护操作即可，而不需要自己操作DOM。

MVP 通过使用 Presenter 来实现对 View 层和 Model 层的解耦。 Presenter 中将 Model 的变化和 View 的变化绑定在一起，以此来实现 View 和 Model 的同步更新。

#### computed和watch的区别

缓存、异步监听等

#### computed和methods的区别

缓存

#### slot

是Vue的内容分发机制，组件内部的模板引擎使用slot元素作为承载分发内容的出口；是子组件的一个模板标签元素

默认插槽，具名插槽和作用域插槽

实现原理：当子组件vm实例化时，获取到父组件传入的slot标签的内容，存放在`vm.$slot`中，默认插槽为`vm.$slot.default`，具名插槽为`vm.$slot.xxx`，xxx 为插槽名，当组件执行渲染函数时候，遇到slot标签，使用`$slot`中的内容进行替换，此时可以为插槽传递数据，若存在数据，则可称该插槽为作用域插槽。

#### 过滤器

把表达式中的值始终当作函数的第一个参数

#### 保存页面的当前状态

组件被卸载：**LocalStorage / SessionStorage**和**路由传值**

组件不被卸载：单页面渲染、Vuex和keep-alive

#### 常见的事件修饰符

.stop, .prevent, .capture, .self, .once, .sync

#### v-if、v-show、v-html 的原理

v-if：addIfCondition方法，生成vnode的时候会忽略对应节点，render的时候就不会渲染；

v-show：会生成vnode，render的时候也会渲染成真实节点，只是在render过程中会在节点的属性中修改show属性值，也就是常说的display；

v-html：先移除节点下的所有节点，调用html方法，通过addProp添加innerHTML属性，归根结底还是设置innerHTML为v-html的值。

#### v-if和v-show的区别

手段、编译过程、编译条件、性能消耗、使用条件

#### v-model 是如何实现的，语法糖实际是什么

表单元素：动态绑定input的value指向message，触发事件时动态把message设为目标值。

组件：利用名为 value 的 prop和名为 input 的事件。双亲组件接收子组件通过input事件$emit出来的数据。本质是一个父子组件通信的语法糖，通过prop和$.emit实现。

#### data为什么是一个函数而不是对象

 对象是引用类型，一次修改会导致其他都变化。vue中组件互不干扰，所以不能写成对象的形式，要写成函数的形式。数据是函数返回值。

#### keep-alive如何实现的，具体缓存什么？

#### $nextTick 原理及作用

nextTick 的核心是利用了如 Promise 、MutationObserver、setImmediate、setTimeout的原生 JavaScript 方法来模拟对应的微/宏任务的实现，本质是为了利用 JavaScript 的这些异步回调任务队列来实现 Vue 框架中自己的异步回调队列。

#### Vue 中给 data 中的对象属性添加一个新的属性时会发生什么？如何解决？

无事发生。。因为在Vue实例创建时，obj.b并未声明，因此就没有被Vue转换为响应式的属性，自然就不会触发视图的更新，这时就需要使用Vue的全局 api **$set()：**

#### Vue中封装数组方法有哪些，如何实现页面更新

push, pop, shift, unshift, splice, sort, reverse

原理：首先获取到这个数组的__ob__，也就是它的Observer对象，如果有新的值，就调用observeArray继续对新的值观察变化（也就是通过`target__proto__ == arrayMethods`来改变了数组实例的型），然后手动调用notify，通知渲染watcher，执行update。

```js
// 缓存数组原型
const arrayProto = Array.prototype;
// 实现 arrayMethods.__proto__ === Array.prototype
export const arrayMethods = Object.create(arrayProto);
// 需要进行功能拓展的方法
const methodsToPatch = [
  "push",
  "pop",
  "shift",
  "unshift",
  "splice",
  "sort",
  "reverse"
];

/**
 * Intercept mutating methods and emit events
 */
methodsToPatch.forEach(function(method) {
  // 缓存原生数组方法
  const original = arrayProto[method];
  def(arrayMethods, method, function mutator(...args) {
    // 执行并缓存原生数组功能（主要是确保this作为上下文）
    const result = original.apply(this, args);
    // 响应式处理
    const ob = this.__ob__;
    let inserted;
    switch (method) {
    // push、unshift会新增索引，所以要手动observer
      case "push":
      case "unshift":
        inserted = args;
        break;
      // splice方法，如果传入了第三个参数，也会有索引加入，也要手动observer。
      case "splice":
        inserted = args.slice(2);
        break;
    }
    // 
    if (inserted) ob.observeArray(inserted);// 获取插入的值，并设置响应式监听
    // notify change
    ob.dep.notify();// 通知依赖更新
    // 返回原生数组方法的执行结果
    return result;
  });
});
```

#### Vue 单页应用与多页应用的区别

SPA单页面应用（SinglePage Web Application）：一个主页面+许多模块组件

MPA多页面应用 （MultiPage Application）：许多完整的页面

#### Vue template 到 render 的过程

template -> ast -> render函数

AST元素节点总共三种类型：type为1表示普通元素、2为表达式、3为纯文本

具体过程：

1. 调用parse方法将template转为ast（抽象语法树）。过程：利用正则表达式顺序解析模板，当解析到开始标签、闭合标签、文本的时候都会分别执行对应的 回调函数，来达到构造AST树的目的。

2. 优化静态节点。生成的DOM永远不会改变

3. 生成代码：generate将ast抽象语法树编译成 render字符串并将静态部分放到 staticRenderFns 中，最后通过 `new Function(`` render``)` 生成render函数。

#### Vue data 中某一个属性的值发生改变后，视图会立即同步执行重新渲染吗？

不会立即同步执行重新渲染。
