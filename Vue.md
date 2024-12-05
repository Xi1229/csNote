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

保存页面的当前状态