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

### **Generator函数**

#### 1. **异步编程**

Generator 函数可以与 Promise 结合使用，使得处理异步操作更加直观。传统的回调函数或 Promise 链可能会导致“回调地狱”，而 Generator 可以通过 `yield` 关键字暂停执行，直到 Promise 被解决。

**示例：** 假设我们有两个需要异步请求的 API。

```
javascript复制代码function fetchData(url) {
  return new Promise((resolve) => {
    setTimeout(() => {
      resolve(`Data from ${url}`);
    }, 1000);
  });
}

function* asyncGenerator() {
  const result1 = yield fetchData('https://api.example.com/data1');
  console.log(result1);
  const result2 = yield fetchData('https://api.example.com/data2');
  console.log(result2);
}

// Helper function to run the generator
function run(generator) {
  const gen = generator();

  function handle(result) {
    if (result.done) return;
    result.value.then((data) => {
      handle(gen.next(data));
    });
  }

  handle(gen.next());
}

run(asyncGenerator);
```

在这个例子中，`fetchData` 函数返回一个 Promise，而 Generator 函数 `asyncGenerator` 使用 `yield` 暂停执行，直到 Promise 被解决。`run` 函数负责处理 Generator 的执行。

#### 2. **迭代器**

Generator 函数可以方便地创建自定义迭代器，使得数据的生成和遍历更加灵活。Generator 会自动实现 `Iterator` 接口。

**示例：** 下面是一个简单的数字生成器，可以无限生成数字。

```
javascript复制代码function* numberGenerator() {
  let i = 0;
  while (true) {
    yield i++;
  }
}

const gen = numberGenerator();
for (let i = 0; i < 5; i++) {
  console.log(gen.next().value); // 输出: 0, 1, 2, 3, 4
}
```

在这个示例中，`numberGenerator` 函数不断生成数字，可以通过 `next()` 方法获取下一个数字。

#### 3. **控制流**

Generator 函数可以用于实现协作式多任务处理，允许你在多个执行点之间切换。通过 `yield`，你可以控制执行顺序，甚至在某些情况下实现类似于线程的行为。

**示例：** 下面是一个简单的任务调度器，使用 Generator 实现任务的分步执行。

```
javascript复制代码function* taskScheduler() {
  console.log('Task 1 started');
  yield; // 暂停执行
  console.log('Task 1 completed');

  console.log('Task 2 started');
  yield; // 暂停执行
  console.log('Task 2 completed');
}

const scheduler = taskScheduler();
scheduler.next(); // 开始 Task 1
scheduler.next(); // 完成 Task 1
scheduler.next(); // 开始 Task 2
scheduler.next(); // 完成 Task 2
```

在这个示例中，`taskScheduler` 生成器函数负责管理多个任务。每次调用 `next()` 都会执行到下一个 `yield`，允许我们控制任务的执行顺序。

#### 总结

- **异步编程**：使用 Generator 函数与 Promise 结合，简化异步操作的处理，使代码更清晰。
- **迭代器**：通过 Generator 创建自定义迭代器，能够灵活地生成和遍历数据。
- **控制流**：Generator 可以用于实现协作式多任务，允许在多个执行点之间控制任务的顺序。

Generator 函数在复杂的程序中提供了更强大的控制能力和可读性，适合多种场景使用。
