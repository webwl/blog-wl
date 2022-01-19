
# 什么是Event Loop
`Event Loop`是一个执行模型，在不同的地方有不同的实现。浏览器和NodeJS基于不同的技术实现了各自的`Event Loop`。

举个栗子，当我们有一个JS文件，里面有很多方法，我们执行这个js文件的时候，文件内容的内容执行过程，就是我们要说的执行模型，因为这个模型执行的特征具有循环表现，所以称为`Event Loop`也就是事件循环


# 宏任务

包括：script ， setTimeout ，setInterval ，setImmediate (Node独有) ，I/O ，UI rendering

# 微任务

包括：process.nextTick(Node独有) ，promise ，Object.observe（已废弃） ，MutationObserver

> MutationObserver接口提供了监视对DOM树所做更改的能力。它被设计为旧的Mutation Events功能的替代品，该功能是DOM3 Events规范的一部分。

关于promise需要注意，promise的回调才是微任务，new Promise()的构造函数是立即执行的

# 执行顺序

我们把事件循环理解的具体一点。分为三个池子，分别为：执行池，宏任务池，微任务池。

执行池为从上往下按顺序执行，宏任务池和微任务池自下而上按push进入的先后顺序执行

- 1:首先我们按顺序执行script中的代码。所有的任务代码现在都在我们的执行池内，我们依次执行。
- 2:遇到普通任务，比如console.log()，我们就直接执行，不用管宏任务和微任务池子。
- 3:如果我们遇到宏任务，比如setTimeout。我们就把回调放入宏任务池子的最底层，依次递增。
- 4:如果我们遇到微任务，我们就把回调放入微任务池子的最底层，依次递增。
- 5:当执行池中的任务执行完毕之后。会优先检查微任务池，如果池子中有任务，将池子最底层的微任务放入执行池，执行里面的内容。如果微任务池内没有任务，则检查宏任务池，如果有任务，就将最底层的宏任务抛入执行池中执行。（注意，执行池执行完成后，总会优先检查微任务池是否清空。）
- 6：当执行池内执行任务时，遇到宏任务或微任务都会会直接抛到对应任务池的最顶层

如此循环执行，知道所有池子都没有任务以后，我们的事件循环就执行完毕了。

尝试一下排序以下例子

示例1：
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

# 当Event Loop遇上async await
尝试排序以下例子

示例2
```
async function async1() {
  console.log('async1 start')
  await async2()
  console.log('async1 end')
}
function async2() {
  console.log('async2 start')
  return Promise.resolve().then(()=>{
    console.log('async2 end')
  })
}
async1()
new Promise(resolve => {
  console.log('new promise')
  resolve()
}).then(function() {
  console.log('promise1')
}).then(function() {
  console.log('promise2')
})

```

提示：
- 1：在于`async1`中`await`函数后面的内容会被封装为一个微任务`Promise.resolve(value).then` 中的回调，但是并不会立即抛到微任务池，后续任务会处于一个“暂停状态”，当`async2`内容完全执行完成，也就是当`async2`中抛出去的微任务也完全执行完成后，`async1`中`await`后封装的微任务才会抛入微任务池。
- 2：在`promise`连续回调的时候只有在上一个回调执行完以后，才会被抛入微任务池

## 难度升级
将`async2`改为`async function`
```
async function async2() {
  console.log('async2 start')
  return Promise.resolve().then(()=>{
    console.log('async2 end')
  })
}
```
修改后，打印内容会改变。尝试排序。


提示：
1：现在调用该函数会返回一个`promise`。这是异步函数的特征之一，它保证函数的返回值为`promise`。

`async2`为普通函数时，结果为
```
async1 start
async2 start
async2 end
promise1
async1 end
promise2
```
`async2`为同步函数时，结果为
```
async1 start
async2 start
async2 end
promise1
promise2
async1 end
```

### 猜想，不确定!
在为`async2`为普通函数时，`await`后面的内容会被封装为一个`Promise`回调，而`async2`为同步函数时，`await`后面的内容会被封装为一个微任务嵌套微任务




# 参考资料
- [MDN-async和await:让异步编程更简单](https://developer.mozilla.org/zh-CN/docs/Learn/JavaScript/Asynchronous/Async_await)
- [当 Event Loop 遇上 async await](https://zhuanlan.zhihu.com/p/121140540)