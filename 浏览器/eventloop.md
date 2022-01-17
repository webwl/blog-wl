
# 什么是eventloop
event loop是一个执行模型，在不同的地方有不同的实现。浏览器和NodeJS基于不同的技术实现了各自的Event Loop。

```
console.log('script start')

setTimeout(function() {
  console.log('setTimeout')
}, 0)

new Promise(resolve => {
  console.log('Promise')
  resolve()
})
  .then(function() {
    console.log('promise1')
  })
  .then(function() {
    console.log('promise2')
  })

console.log('script end')
```
从上面例子中，我们可以粗略看到

# 宏任务

包括：script ， setTimeout ，setInterval ，setImmediate (Node独有) ，I/O ，UI rendering

# 微任务

包括：process.nextTick(Node独有) ，promise ，Object.observe（已废弃） ，MutationObserver

> MutationObserver接口提供了监视对DOM树所做更改的能力。它被设计为旧的Mutation Events功能的替代品，该功能是DOM3 Events规范的一部分。

# 执行顺序

### 图谱解释：

- 执行同步代码，这属于宏任务。这些同步代码有一些是同步语句，有一些是异步语句（比如setTimeout等）
- 执行栈为空，查询是否有微任务需要执行
- 执行所有微任务
- 必要的话渲染 UI
- 然后开始下一轮 Event loop，执行宏任务中的异步代码

### 个人理解：

我们把事件循环理解的具体一点。分为三个池子，分别为：执行池，宏任务池，微任务池。
（script本质上就是一个宏任务，只是为了形象一点，把script称为执行池）

- 1:首先我们按顺序执行script中的代码。所有的任务代码现在都在我们的执行池内，我们依次执行。
- 2: 遇到普通任务，比如console.log()，我们就直接执行，不用管宏任务和微任务池子。
- 3:如果我们遇到宏任务，比如setTimeout。我们就把回调放入宏任务池子的最底层，依次递增。
- 4:如果我们遇到微任务，我们就把回调放入微任务池子的最底层，依次递增。
- 5:当执行池中的任务执行完毕之后。我们去检查微任务池，如果池子中有任务，那么从最底层开始，往上依次执行，直到当前微任务池清空。
- 6：当微任务池清空之后，开始执行宏任务池子中最底层的任务。
- 7：如果在执行宏任务或者微任务的过程当中，内部又出现了宏任务或者微任务，择继续往宏任务或者微任务池子push。
- 8：当执行完当前宏任务后，立刻又转回去检查微任务池子是否清空，如果有任务，继续执行微任务，直到微任务完成之后，再去检查宏任务池子是否有任务，如果有，择执行最底层的那个任务。如此循环往复，这就是为什么叫做loop的原因。

我们通过以下掘金的一个示例来解析，生动又形象
```
console.log(1);

setTimeout(() => {
  console.log(2);
  Promise.resolve().then(() => {
    console.log(3)
  });
});

new Promise((resolve, reject) => {
  console.log(4)
  resolve(5)
}).then((data) => {
  console.log(data);
  setTimeout(() => {
      console.log(8);
      Promise.resolve().then(() => {
        console.log(9)
      });
    });
})

setTimeout(() => {
  console.log(6);
})

console.log(7);

```
### 示例解析
#### 第一步：
```
console.log(1);
// 任务池：[console]
// 宏任务池: []
// 微任务池: []
// 打印结果：1
```
#### 第二步：
```
setTimeout(() => {
    // 这个回调函数叫做callback1，setTimeout属于宏任务，所以放到宏任务池中
  console.log(2);
  Promise.resolve().then(() => {
    console.log(3)
  });
});
// 任务池：[setTimeout]
// 宏任务池: [callback1]
// 微任务池: []
// 打印结果：1
```
#### 第三步：
```
new Promise((resolve, reject) => {
  // 注意，这里是同步执行的，
  console.log(4)
  resolve(5)
}).then((data) => {
 // 这个回调函数叫做callback2，promise属于微任务，所以放到微任务池中
  console.log(data);
  setTimeout(() => {
      console.log(8);
      Promise.resolve().then(() => {
        console.log(9)
      });
    });
})

// 任务池：[Promise]
// 宏任务池: [callback1]
// 微任务池: [callback2]
// 打印结果：1,4
```
#### 第四步：
```
setTimeout(() => {
 // 这个回调函数叫做callback3，setTimeout属于宏任务，所以放到宏任务池中
  console.log(6);
})

// 任务池：[setTimeout]
// 宏任务池: [callback1,callback3]
// 微任务池: [callback2]
// 打印结果：1,4
```
#### 第五步：
```
console.log(7);

// 任务池：[console]
// 宏任务池: [callback1,callback3]
// 微任务池: [callback2]
// 打印结果：1,4,7
```

好啦，全局Script代码执行完了，也就是说，我们的任务池已经清空了。进入下一个步骤，从微任务池中依次取出任务执行，直到微任务池子为空。

#### 第六步：
```
console.log(data); // 这里data是Promise的决议值5
setTimeout(() => {
    // 这个回调函数叫做callback4，setTimeout属于宏任务，所以放到宏任务池中
  console.log(8);
  Promise.resolve().then(() => {
    console.log(9)
  });
});

// 任务池：[callback2]
// 宏任务池: [callback1,callback3,callback4]
// 微任务池: []
// 打印结果：1,4,7,5
```

微任务池子中只有一个callback2任务，执行完了之后，我们就进入下一步，从宏任务中去执行最底层的宏任务。

#### 第七步：
```
  console.log(2);
  Promise.resolve().then(() => {
    // 这个回调函数叫做callback5，promise属于微任务，所以放到微任务池中
    console.log(3)
  });
// 任务池：[callback1]
// 宏任务池: [callback3,callback4]
// 微任务池: [callback5]
// 打印结果：1,4,7,5,2
```
#### 第八步：
```
console.log(3);
// 任务池：[callback5]
// 宏任务池: [callback3,callback4]
// 微任务池: []
// 打印结果：1,4,7,5,2,3
```
#### 第九步：
```
console.log(6);
// 任务池：[callback3]
// 宏任务池: [callback4]
// 微任务池: []
// 打印结果：1,4,7,5,2,3,6
```
#### 第十步：
```
console.log(8);
Promise.resolve().then(() => {
    // 这个回调函数叫做callback6，promise属于微任务，所以放到微任务池中
    console.log(9)
});
// 任务池：[callback4]
// 宏任务池: []
// 微任务池: [callback6]
// 打印结果：1,4,7,5,2,3,6,8
```
#### 第十一步：
```
 console.log(9)
// 任务池：[callback6]
// 宏任务池: []
// 微任务池: []
// 打印结果：1,4,7,5,2,3,6,8,9
```

