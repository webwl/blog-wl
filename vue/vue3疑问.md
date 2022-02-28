# new Vue 与 createApp
从new一个实例对象，到使用一个实例API来实现实例化，有什么优势？
## new Vue
```
Vue.component('test-component', {
  template: '<p>test-component</p>'
})

const vue_a = new Vue({
  template: `
    <div>
      vue_a
      <test-component></test-component>
    </div>
  `
})
vue_a.$mount('#app_a')

const vue_b = new Vue({
  template: `
    <div>
      vue_b
      <test-component></test-component>
    </div>
  `
})
vue_b.$mount('#app_b')

```

在vue2的组件系统设计中，所有的vue实例是共享一个Vue构造函数对象（包括全局指令/全局组件等），无法做到完全隔离。

## createApp
```


const vue_a = Vue.createApp({
  template: `
    <div>
      vue_a
      <test-component></test-component>
    </div>
  `
})
vue_a.component('test-component', {
  template: '<p>test-component</p>'
})

vue_a.mount('#app_a')



const vue_b = Vue.createApp({
  template: `
    <div>
      vue_b
    </div>
  `
})
vue_b.mount('#app_b')

```
使用vue3的实例方法createApp来进行实例创建，可以完全分隔实例A与实例B的环境，不会存在挂载一个全局组件时，全局污染。