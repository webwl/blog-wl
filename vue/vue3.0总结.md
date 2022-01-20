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
ref本质上就是reactive，ref(obj)等价于reactive({value: obj})

reactive则不需要铜鼓value去取，本身就已经是一个对象了，不用再去封装。

reactive添加响应性后不可解构，否则失去响应性

## ref与toRef，toRefs的区别

解构会使reactive设置的响应性消失。如果我们在setup中想要导出一个解构的响应对象该怎么办？

这个时候就可以用到toRefs,toRefs可以使对象失去响应性，但是对象里的内容保持响应性。
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
toRef就是toRefs的单个版本,可以为对象的某一个内容保持响应性`toRef(data, 'title')`


ref不会导致原始数据改变，toRef和toRefs会，可以理解为一个是深拷贝，一个是浅拷贝
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
我们使用ref或reactive创建深拷贝了一份内容用来做响应式，如果我们希望获取到这个响应式对象的原始数据该怎么办？可以通过toRaw获取ref或reactive引用的原始数据。
```
let obj1 = {...};
//state和obj1是引用关系，state的本质是一个Proxy对象，其中引用了obj1
let state = reactive(obj1);
//通过toRaw方法获取到原始数据，其实是获取到obj1的内存地址，obj2和obj1是完全相等的
let obj2 = toRaw(state)
console.log(obj1 === obj2);//true

```
有些同学会问，那直接使用obj1来修改数据不就行了吗？可关键是我们在使用reactive定义数据时一般不会先定义一个obj，再将他传给reactive，都是直接在reactive中写数据的。

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

# 参考资料
- [官网-组合式API教程](https://v3.cn.vuejs.org/guide/composition-api-introduction.html#%E4%BB%80%E4%B9%88%E6%98%AF%E7%BB%84%E5%90%88%E5%BC%8F-api)
- [官网-响应性API](https://v3.cn.vuejs.org/api/reactivity-api.html)