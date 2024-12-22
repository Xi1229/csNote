## Vue基础

### 基本原理

Object.defineProperty（vue3.0使用proxy ）将它们转为 getter/setter

### 双向数据绑定的原理

vue2中，`Observer` 通过 `Object.defineProperty` 劫持每个数据属性的 `getter` 和 `setter`，当数据发生变化时（即 `setter` 被触发），`setter` 会通知 `Dep`，然后通过 `Dep` 通知notify相应的 `Watcher`。

数据劫持 和 发布者-订阅者模式

Object.defineProperty()劫持各属性setter，getter，在数据变动时发布消息给订阅者，触发相应的监听回调。

observer：劫持监听data、响应式数据和数组操作

compile：将模板解析为render函数，编译器为渲染函数创建了一个渲染 Watcher。Watcher 在执行渲染函数时，渲染函数中访问响应式数据时，会触发响应式数据的 `getter`。在 `getter` 中，Vue 会检查当前是否有活跃的 `Watcher`（即 `Dep.target` 是否不为 `null`）。如果存在，调用 `D`

`ep.addDep()`，将当前 `Watcher` 添加到 `Dep` 的依赖列表中。`Watcher` 被记录在 `Dep` 中，成为该属性的依赖。当属性发生变化时，`Dep` 可以通知所有依赖（`Watcher`）更新。

dep：每个响应式属性都对应一个 `Dep` 实例。管理依赖，记录哪些 `Watcher` 依赖了某个数据属性，并在数据变化时通知这些 `Watcher` 进行更新

总结：observer监听劫持，setter被触发便通知dep，再notiy给watcher，再更新到视图。compile编译成渲染函数，创建watcher，访问数据触发getter，调用dep.depend收集依赖，depend方法里调用Dep.target.addDep(this)确保当前的Dep被当前的watcher收集到，Watcher.addDep(dep) 完成依赖的双向绑定，将watcher添加到dep依赖，将当前dep添加到watcher依赖列表。属性变化时，dep通知watcher更新

### Object.defineProperty()数据劫持的缺点

有些操作无法拦截。vue3使用proxy解决这件事

### MVVM、MVC、MVP的区别

MVC单向循环  观察者模式

MVVM ViewModel联系Model和View 双向数据绑定=》开发者只需要专注于数据的维护操作即可，而不需要自己操作DOM。

MVP 通过使用 Presenter 来实现对 View 层和 Model 层的解耦。 Presenter 中将 Model 的变化和 View 的变化绑定在一起，以此来实现 View 和 Model 的同步更新。

### computed和watch的区别

缓存、异步监听等

## computed和methods的区别

缓存

### slot

是Vue的内容分发机制，组件内部的模板引擎使用slot元素作为承载分发内容的出口；是子组件的一个模板标签元素

默认插槽，具名插槽和作用域插槽

实现原理：当子组件vm实例化时，获取到父组件传入的slot标签的内容，存放在`vm.$slot`中，默认插槽为`vm.$slot.default`，具名插槽为`vm.$slot.xxx`，xxx 为插槽名，当组件执行渲染函数时候，遇到slot标签，使用`$slot`中的内容进行替换，此时可以为插槽传递数据，若存在数据，则可称该插槽为作用域插槽。

### 过滤器

把表达式中的值始终当作函数的第一个参数

### 保存页面的当前状态

组件被卸载：**LocalStorage / SessionStorage**和**路由传值**

组件不被卸载：单页面渲染、Vuex和keep-alive

### 常见的事件修饰符

.stop, .prevent, .capture, .self, .once, .sync

### v-if、v-show、v-html 的原理

v-if：addIfCondition方法，生成vnode的时候会忽略对应节点，render的时候就不会渲染；

v-show：会生成vnode，render的时候也会渲染成真实节点，只是在render过程中会在节点的属性中修改show属性值，也就是常说的display；

v-html：先移除节点下的所有节点，调用html方法，通过addProp添加innerHTML属性，归根结底还是设置innerHTML为v-html的值。

### v-if和v-show的区别

手段、编译过程、编译条件、性能消耗、使用条件

### v-model 是如何实现的，语法糖实际是什么

表单元素：动态绑定input的value指向message，触发事件时动态把message设为目标值。

组件：利用名为 value 的 prop和名为 input 的事件。双亲组件接收子组件通过input事件$emit出来的数据。本质是一个父子组件通信的语法糖，通过prop和$.emit实现。

### data为什么是一个函数而不是对象

 对象是引用类型，一次修改会导致其他都变化。vue中组件互不干扰，所以不能写成对象的形式，要写成函数的形式。数据是函数返回值。

### keep-alive如何实现的，具体缓存什么？

### $nextTick 原理及作用

nextTick 的核心是利用了如 Promise 、MutationObserver、setImmediate、setTimeout的原生 JavaScript 方法来模拟对应的微/宏任务的实现，本质是为了利用 JavaScript 的这些异步回调任务队列来实现 Vue 框架中自己的异步回调队列。

### Vue 中给 data 中的对象属性添加一个新的属性时会发生什么？如何解决？

无事发生。。因为在Vue实例创建时，obj.b并未声明，因此就没有被Vue转换为响应式的属性，自然就不会触发视图的更新，这时就需要使用Vue的全局 api **$set()：**

### Vue中封装数组方法有哪些，如何实现页面更新

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

### Vue 单页应用与多页应用的区别

SPA单页面应用（SinglePage Web Application）：一个主页面+许多模块组件

MPA多页面应用 （MultiPage Application）：许多完整的页面

### Vue template 到 render 的过程

template -> ast -> render函数

AST元素节点总共三种类型：type为1表示普通元素、2为表达式、3为纯文本

具体过程：

1. 调用parse方法将template转为ast（抽象语法树）。过程：利用正则表达式顺序解析模板，当解析到开始标签、闭合标签、文本的时候都会分别执行对应的 回调函数，来达到构造AST树的目的。

2. 优化静态节点。生成的DOM永远不会改变

3. 生成代码：generate将ast抽象语法树编译成 render字符串并将静态部分放到 staticRenderFns 中，最后通过 `new Function(`` render``)` 生成render函数。

### Vue data 中某一个属性的值发生改变后，视图会立即同步执行重新渲染吗？

不会立即同步执行重新渲染，而是进行异步更新机制（将更新任务放入一个队列中，延迟到下一次事件循环时统一执行）

原因：避免重复计算，合并多个更新。

实现细节：

1. 数据变化，触发getter/setter；
2. setter通知依赖的dep，将相关的watcher推入更新队列。
3. vue开启一个队列来手机所有的watcher，并去重，保证每个watcher只执行一次。
4. 在下一个事件循环（tick）中，Vue 使用 nextTick 刷新队列，执行 watcher 的 update 方法，完成视图更新。

### **什么时候判定watcher收集完成？**

1. Watcher 收集依赖的结束时机是 **渲染函数或计算属性逻辑执行完成**。

2. Vue 通过设置和清除 Dep.target 确保依赖收集的准确性。

3. 每次收集之前，Vue 会清理旧的依赖，避免动态依赖带来的问题。

Vue 在执行渲染函数（或计算属性）时，会逐一访问这些响应式属性。当渲染函数或计算属性执行完毕时，Vue 会将 Dep.target 设置为 null。此时，Watcher 的收集工作完成，后续对响应式属性的访问不会再被记录到该 Watcher 中，避免无关的依赖被错误收集。

### mixin和extends的覆盖逻辑

都是通过mergeOptions方法合并。

mixins接收混入对象的数组，混入对象合并到一种选项中。

extends接收一个对象或者构造函数

共同点：先按顺序执行mixin中的生命周期钩子/watch，再执行组件钩子/watch。

#### **mergeOptions的执行过程**

1. 规范化选项（normalizeProps、normalizelnject、normalizeDirectives)

2. 对未合并的选项，进行判断

   

3. 合并处理。根据一个通用 Vue 实例所包含的选项进行分类逐一判断合并，如 props、data、 methods、watch、computed、生命周期等，将合并结果存储在新定义的 options 对象里。

4. 返回合并的options

### 描述vue自定义指令（directive）

一般对dom元素进行底层操作时会用，尽量只操作dom展示，不修改内部的值。如果要修改，需要使用keydown事件。

**全局定义、局部定义；**

**钩子函数**（bind、inSerted、update、ComponentUpdate、unbind）

**钩子函数参数**【el、binding指令核心对象（name、value、oldValue、expression、arg、modifers）vnode、oldVnode】

​	•	expression：字符串形式的绑定表达式，比如 v-bind:prop="value" 中的 "value".

​	•	arg：字符串形式的指令参数，比如 v-bind:prop.sync 中的 "sync".

​	•	modifiers：一个对象，包含所有的修饰符。修饰符是在指令名后加上点号（.）表示的，如 .stop 或 .prevent。

使用场景：鼠标聚焦、下拉菜单、相对时间转换，滚动动画、实现图片懒加载、集成第三方插件

### 子组件可以直接改变双亲组件的数据

不可以。因为单向数据流。

解决方案：通过$emit派发自定义事件，双亲组件接收并修改

### Vue如何收集依赖？

data初始化时（即将普通对象变为响应式对象）会进行依赖📱：实例化Dep，get函数通过dep.depend进行依赖收集

1. Dep是一个class， static target指向全局唯一一个wathcer，保证了同一时间全局只有一个 watcher 被计算（因为是一个单线程、同步的过程），另一个属性 subs 则是一个 Watcher 的数组，所以 Dep 实际上就是对 Watcher 的管理。

watcher一般都是响应式数据被模版、computed或watch使用时，会有对应的watcher。但是每一个带有getter/setter的响应式数据都有dep。但没有watcher的响应式数据的dep没有订阅者（空依赖）。

**过程**

1. 初始化，调用defineReactive，其中getter收集依赖。
2. 到mount过程，实例化watcher，执行watcher的this.get()
3. get调用pushTarget ，把 Dep.target 赋值为当前的 watcher。this.getter.call（vm，vm），这里的 getter 会执行 vm._render() 方法，在这个过程中便会触发数据对象的 getter。
4. 这时，每个对象值的 getter 都持有一个 dep，在触发 getter 的时候会调用 dep.depend() 、执行Dep.target.addDep(this)，执行addDep，到dep.addSub

注意：在 vm._render() 过程中，会触发所有数据的 getter

### webpack和vite的区别

| 特性         | Vite                                                         | **Webpack**                                                  |
| ------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 开发模式原理 | 使用原生 ES 模块 (ESM) 的浏览器支持，按需加载模块，无需对整个项目进行预构建。 | 使用 `bundle` 的方式，将所有模块打包成一个或多个文件供浏览器使用。 |
| 启动速度     | 快速启动，尤其是大项目，因其无需预先打包所有模块。           | 大项目启动速度较慢，因为需要先将整个项目进行一次完整打包。   |
| HMR 性能     | 热更新速度极快，仅更新修改的模块，因为利用了浏览器的 ESM 缓存机制。 | 热更新性能稍逊色，需处理模块依赖关系，重新生成部分 bundle。  |
| 构建工具     | 使用 `esbuild` (用 Go 编写，速度极快) 进行依赖预构建。       | 使用 JavaScript 编写的 `Webpack` 构建模块，构建速度较慢。    |
| 配置复杂度   | 配置简单，开箱即用，支持直接导入 TypeScript、Vue SFC、CSS 等。 | 配置复杂，需要设置多种 Loader 和 Plugin 才能实现同样的功能。 |
| 生产模式     | 生产模式基于 Rollup，输出更优化且更小的 bundle。             | 生产模式使用内置的构建逻辑，可以灵活定制，但可能输出文件更大。 |
| 生态支持     | 生态逐渐丰富，但较 Webpack 更年轻，部分第三方库可能需要调整以适配。 | 生态成熟，几乎所有主流前端库和工具都有 Webpack 插件或兼容支持。 |
| 学习成本     | 学习曲线平缓，默认配置已覆盖大部分场景，插件系统简单易懂。   | 学习曲线较陡，需要了解模块打包的细节、Loader、Plugin 的配置和优化方法。 |
| 适用场景     | 适合现代框架开发（Vue 3、React 等），尤其是大规模、多页面或模块化项目开发中的开发环境优化。 | 适合任何前端项目，尤其是需要高定制化、复杂依赖的项目，兼容性较强。 |

##### 原生ES模块

静态结构、异步加载、独立作用域、支持浏览器原生加载

##### bundle

将多个模块、资源、代码片段等打包成一个或多个文件的过程。捆绑是为了提高性能、减少 HTTP 请求（多个js文件合成一个文件）并优化代码的加载时间（压缩、优化、按需加载）。

##### Rollup

js模块打包工具，针对ESM进行优化。静态分析和代码优化技术，生成更小体积和更高性能的打包文件。

特点：Tree Shaking、小而快的输出、支持多种输出格式、插件系统。

在vite中的角色：存在与生产模式（构建时），对项目代码进行静态分析和优化，生成最终的生产构建文件。

### React和Vue的异同

同：

- 注意核心库，外包其他功能（路由、全局状态管理）给相关库
- 自带构建工具
- Virtual DOM提高重绘性能
- props概念，组件传递数据
- 组件化

异：

- 数据流：都是单向数据流，但是vue指出数据双向绑定
- 虚拟dom：vue跟踪每个组件的依赖关系；react的应用状态改变导致子组件重新渲染（可以通过PureComponent/shouldComponentUpdate生命周期控制）
- 组件化：vue=》html模板；react=》JSX（react中的render函数支持闭包，可以直接调用import组件；vue中数据需要挂在this上中转一次，import之后需要在components中再声明一下）
- 监听数据变化的实现原理：vue=>数据劫持；react通过引用，可能导致大量不必要的vDOM的重新渲染，需要PureComponent/shouldComponentUpdate优化。vue可变数据，react强调不可变。
- 高阶组件：react高阶组件（高阶函数）扩展，vue使用mixins；
- 构建工具：Create React APP和vue-cli
- 跨平台：React Native和Weex

### Vue的优点

- 轻量级框架：只关注视图层，构建数据的视图集合；
- 双向数据绑定
- 组件化：保留了 `react` 的优点，实现了 `html` 的封装和重用；
- 视图，数据，结构分离：简化数据更改；
- 虚拟DOM
- 运行速度更快

### assets和static的异同

相同点：存放静态资源；

不同点：

- assets打包压缩体积后的文件放入static文件，和index.html一同上传服务器
- static不需要打包压缩格式化，直接进入打包好的目录，因此体积更大；

注意：第三方资源文件如`iconfoont.css` 等文件可以放置在 `static` 中，因为这些引入的第三方文件已经经过处理，不再需要处理，直接上传。

### delete和Vue.delete删除数组的区别

delete 只是被删除的元素变成了 empty/undefined；`Vue.delete` 直接删除了元素，改变了数组的键值。

### vue如何监听对象或者数组某个属性的变化

- this.$set(你要改变的数组/对象，你要改变的位置/key，你要改成什么value)
  - 数组：使用splice触发响应式
  - 对象：判断属性是否存在、对象是否是响应式，调用defineReactive 
- splice()、 push()、pop()、shift()、unshift()、sort()、reverse()  缓存重写

### Vue模版编译原理

模板编译：template转化成一个JavaScript函数（template无法被浏览器解析并渲染）。浏览器执行函数并渲染成HTML元素。

解析阶段：使用大量的正则表达式对template字符串进行解析，将标签、指令、属性等转化为抽象语法树AST。

优化阶段：遍历AST，找到其中的一些静态节点并进行标记，方便在页面重渲染的时候进行diff比较时，直接跳过这一些静态节点，优化runtime的性能。

生成阶段：将最终的AST转化为render函数字符串。

### SSR

服务端渲染，将把标签渲染成HTML，返回html给客户端

优势：SEO、首屏加载速度快；

缺点：

- 服务端渲染只支持beforeCreate和created两个钩子；
- 当需要一些外部扩展库时需要特殊处理，服务端渲染应用程序也需要处于Node.js的运行环境；
- 更多的服务端负载。

### Vue的性能优化

#### 编码阶段

- 尽量减少data中的数据，data中的数据会增加getter和setter，会收集对应的watcher

- v-if和v-for不能连用

- 如果需要使用v-for给每项元素绑定事件时，使用事件代理

- SPA页面采用keep-alive缓存组件

- 在更多情况下，使用v-if代替v-show

- key保证唯一
- 使用路由懒加载、异步组件
- 防抖、节流
- 第三方模块按需导入
- 长列表滚动到可视区域动态加载
- 图片懒加载

#### SEO优化

- 预渲染
  - 使用prerender-spa-plugin在项目构建时生成每个路由的静态HTML文件（适用于小型网站或者部分页面）
- 服务端渲染SSR
  - 主要流程：客户端请求HTML页面，服务端渲染HTML发送给浏览器，客户端激活Vue应用
  - 具体流程
    - 创建客户端（挂载vue，**entry-client.js**）和服务端入口（渲染vue，**entry-server.js**）
    - main.js创建工厂函数，以供服务端和客户端共用
    - server.js创建nodejs服务，处理ssr渲染
    - 打包配置。创建两个webpck配置文件，用于打包客户端和服务端
    - 构建命令打包
    - 部署：客户端代码上传至cdn或静态服务器；服务端在服务器上运行server.js，使用PM2或Docker部署
  - 页面瞬间可见，提升首屏渲染速度
  - 客户端和服务端使用相同的路由配置，水合

#### 打包优化

- 压缩代码
- Tree Shaking/Scope Hoisting
  - Scope Hoisting：
    - 静态分析模块，确定依赖关系；合并作用域（多个模块无相互作用）；消除包装；
    - rollup默认开启；webpack需启用production和optimization.moduleConcatenation
  - Tree Shaking移除未使用代码
    - 缺点：动态导入无法工作；有副作用的代码依然会保留。
- 使用cdn加载第三方模块
  - HTML中使用script加载
  - 配置webpack或者CDN
    - webpack：html-webpack-plugin插件；修改webpack配置文件；在index.html引入；
    - vite：vite-plugin-cdn-import；修改vite配置文件；在index.html中华使用
- 多线程打包happypack
  - happypack是加速webpack打包的工具，通过使用多核CPU将打包任务分发多线程
  - 原理：
    - 创建多线程工作池
    - 主线程与子线程协作
      - 主线程分发至线程池；线程池处理后返回主线程
    - 加速构建
  - webpack5自带thread-loader，减少happypack的依赖
- splitChunks抽离公共文件
  - 将重复使用的代码提取至独立的文件
  - 提升缓存效果
  - 提高加载速度
- sourceMap优化
  - sourceMap是一个将压缩、混淆后的代码与原始代码关联的文件，以便调试压缩后的代码
  - 生产环境禁用sourcemap
  - 分离sxourcemap，生成单独.map文件，而不是嵌入js或css，客户端就不必下载sourcemap文件

#### 用户体验

- 骨架屏
- PWA：渐进式网络应用程序；核心技术（Service Worker、Web App Manifest、HTTPS）
- 缓存（客户端缓存、服务端缓存）优化、服务端开启gzip压缩；
  - 客户端缓存
    - 浏览器缓存（强制本地缓存、协商缓存）
    - **LocalStorage 和 SessionStorage**
    - IndexDB：浏览器内建的NoSQL数据库，存储大量结构化数据，适合复杂的客户端应用
    - PWA的Service Worker拦截网络请求并缓存资源，实现离线功能和资源优化
  - 服务端缓存
    - HTTP响应头
    - 数据库缓存查询：redis
    - 反向代理
    - 应用级缓存
    - CDNs缓存

