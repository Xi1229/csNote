**一、基础知识**

​	1.	**React Native 的工作原理是什么？**

**答：**

React Native 使用 JavaScript 和原生代码通过 JS Bridge 通信：

​	•	**JS 线程** 执行 JavaScript 代码，并使用 React 构建 UI。

​	•	**原生线程** 渲染平台原生的 UI 组件。

​	•	**Bridge** 在两者之间传递数据。

例如，用户点击按钮触发事件，事件会通过 Bridge 通知 JS 线程执行逻辑，并将结果返回原生端渲染。

​	2.	**React Native 与 React 的区别是什么？**

**答：**

​	•	React 用于 Web 开发，基于 DOM 渲染；React Native 用于移动端开发，渲染原生组件。

​	•	React Native 提供了特定组件，如 <View>、<Text> 和 <Image>，代替了 Web 的 <div>、<span> 和 <img>。

​	•	React Native 没有直接的 CSS 支持，样式使用 StyleSheet。

​	3.	**如何处理 React Native 的生命周期？**

**答：**

​	•	React Native 遵循 React 的生命周期方法，如 componentDidMount 和 useEffect。

​	•	对于组件挂载时初始化数据，可在 componentDidMount 或 useEffect 中完成。

​	•	对于屏幕切换时，配合 **React Navigation** 的 useFocusEffect，在页面获取焦点时加载数据。

## metro

js打包工具

文件打包、模块解析、热更新、文件监听、代码优化

### metro.config.js的常见配置

定义打包时的自定义行为

#### resolver

如何解析模块

定义metro支持的文件类型

#### transformer

转换文件内容

配置代码转换行为，例如是否启用Babel或支持SVG转换

#### watchFolders

指定metro应该监听的额外文件夹

#### server

配置Metro本地服务器的行为，比如更改默认服务器端口等网络配置。

#### serializer

配置bundle的序列化行为，比如跳过不必要的Babel配置