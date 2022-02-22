
# 什么是this

this是JS中的一个关键字，它通常代表着你要进行操作的对象的引用。使用它可以更加优雅的调用它自身的方法或者属性。

# this的指向

- 误解1：从字面上来看，很容易理解为this就是指向函数自身。
- 误解2：this指向函数的作用域
- 正确答案：每个喊得this实在调用时绑定的，完全取决于函数的调用位置。

## 误解1：
```
function foo(num){
    console.log("foo:" + num);
    this.count++;
}
foo.count = 0;

var i;
for(i=0;i<10;i++){
    if(i>5){
        foo(i);
    }
}

console.log(foo.count) //0
```

显而易见，可以打印出4条log，但是foo.count却等于0。因为，在这个时候foo是在for循环中调用的，此时的this并不指向foo。

遇到这种情况，我们可以用这种方式来解决。

```
//第一种

function foo(num){
    console.log("foo:" + num);
    foo.count++;
}
这种方式回避了this的问题，完全依赖于foo的词法作用域

//第二种
for(i=0;i<10;i++){
    if(i>5){
        foo.call(foo,i);
    }
}
这种方式让我强制的修改了this的指向
```
## 误解2

```
function foo(){
    var a = 2;
    this.bar();
}
function bar(){
    console.log(this.a);
}
foo(); //a is not defined
```
这个例子试图通过作用域来联系foo与bar，但实际上this的指向却没有因为作用域而改变，而是指向最初的this指向window。

# 箭头函数
箭头函数没有自己的this对象
