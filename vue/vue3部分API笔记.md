- [响应性API](#响应性api)
  - [ref和reactive的区别](#ref和reactive的区别)
    - [TODO 查一下ref和reactive的实现方式不同，一个是new class，一个是proxy](#todo-查一下ref和reactive的实现方式不同一个是new-class一个是proxy)
    - [reactive赋值](#reactive赋值)
      - [对象](#对象)
      - [数组](#数组)
  - [ref与toRef，toRefs的区别](#ref与toreftorefs的区别)
  - [toRaw,markRaw](#torawmarkraw)
    - [toRaw](#toraw)
    - [markRaw](#markraw)
- [setup](#setup)
  - [生命周期](#生命周期)
- [参考资料](#参考资料)

# 响应性API
## ref和reactive的区别

都是为了给对象添加响应性。
ref配置基础数据类型，reactive配置复合数据类型。

> 如果将对象分配为 ref 值，则它将被 reactive 函数处理为深层的响应式对象。

从官方文档看，其实可以一直用ref，为什么要区分开，我估计为了理解方便？因为如果是基础数据类型，会被封装为一个对象
```
const counter = ref(0)

console.log(counter) // { value: 0 }
console.log(counter.value) // 0
```
ref本质上就是`reactive，ref(obj)`等价于`reactive({value: obj})`

`reactive`则不需要通过`value`去取，本身就已经是一个对象了，不用再去封装。

`reactive`添加响应性后不可解构，否则失去响应性

###   TODO 查一下ref和reactive的实现方式不同，一个是new class，一个是proxy

### reactive赋值
#### 对象
当将 `ref` 分配给 `reactive property` 时，`ref` 将被自动解包
```
const count = ref(1)
const obj = reactive({})

obj.count = count

console.log(obj.count) // 1
console.log(obj.count === count.value) // true
```
#### 数组
```
// 方案1：创建一个响应式对象，对象的属性是数组
const state = reactive({
    arr: []
});
state.arr = [1, 2, 3]

// 方案2: 使用ref函数
const state = ref([])
state.value = [1, 2, 3]

// 方案3: 使用数组的push方法
const arr = reactive([])
arr.push(...[1, 2, 3])
```

## ref与toRef，toRefs的区别

解构会使`reactive`设置的响应性消失。如果我们在`setup`中想要导出一个解构的响应对象该怎么办？

这个时候就可以用到`toRefs,toRefs`可以使对象失去响应性，但是对象里的内容保持响应性。
```
setup(){
  const data = reactive({
    title: 'vue3',
    msg: '好玩'
  })
  const dataRefs = toRefs(data)
  return {
    ...dataRefs
  }
}
```
`toRef`就是`toRefs`的单个版本,可以为对象的某一个内容保持响应性`toRef(data, 'title')`


`ref`不会导致原始数据改变，`toRef`和`toRefs`会，可以理解为一个是深拷贝，一个是浅拷贝
```setup(){
  const a = 0
  let refData = ref(a)
  const b = {
    title: 'vue3',
    msg: '好玩'
  }
  const toRefData = toRef(b, 'title)
  refData++
  console.log('refData:', refData) // 1
  console.log('a:', a) // 0
  
  b.title = 'zs'
  console.log('toRefData:', toRefData.title) // zs
  console.log('b:', b.title) // zs
}

```

## toRaw,markRaw
### toRaw
我们使用`ref`或`reactive`创建深拷贝了一份内容用来做响应式，如果我们希望获取到这个响应式对象的原始数据该怎么办？可以通过`toRaw`获取`ref`或`reactive`引用的原始数据。
```
let obj1 = {...};
//state和obj1是引用关系，state的本质是一个Proxy对象，其中引用了obj1
let state = reactive(obj1);
//通过toRaw方法获取到原始数据，其实是获取到obj1的内存地址，obj2和obj1是完全相等的
let obj2 = toRaw(state)
console.log(obj1 === obj2);//true

```
有些同学会问，那直接使用`obj1`来修改数据不就行了吗？可关键是我们在使用`reactive`定义数据时一般不会先定义一个`obj`，再将他传给`reactive`，都是直接在`reactive`中写数据的。

### markRaw
标记一个对象，使其永远不会转换为 proxy。返回对象本身。
```
const foo = markRaw({})
console.log(isReactive(reactive(foo))) // false

// 嵌套在其他响应式对象中时也可以使用
const bar = reactive({ foo })
console.log(isReactive(bar.foo)) // false
```
也就是说，可以通过markRaw创建一个，不能被添加响应性的对象

# setup
## 生命周期
setup内部的生命周期和选项式API差别不大

![官方setup生命周期对比](https://github.com/webwl/blog-wl/blob/dev/images/vue/setup%E7%94%9F%E5%91%BD%E5%91%A8%E6%9C%9F%E5%AF%B9%E6%AF%94.png)

无法使用

# 参考资料
- [官网-组合式API教程](https://v3.cn.vuejs.org/guide/composition-api-introduction.html#%E4%BB%80%E4%B9%88%E6%98%AF%E7%BB%84%E5%90%88%E5%BC%8F-api)
- [官网-响应性API](https://v3.cn.vuejs.org/api/reactivity-api.html)