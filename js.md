mdn   web docs

## JavaScript

### instanceof

```js
function myInstanceof(left, right) {
    // 获取对象的原型
    let proto = Object.getPrototypeOf(left);
    // 获取构造函数的prototype对象
    let prototype = right.prototype;
    while(true) {
        // 判断原型链是否已经遍历到了最顶端
        if (!proto) return false;
        if (proto === prototype) return true;
        // 如果没有找到，就继续从其原型上找，Object.getPrototypeOf方法用来获取指定对象的原型
        proto = Object.getPrototypeOf(proto);
    }
}
```

