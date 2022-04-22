
# 创建多个应用
关于多实例应用，有什么好处？应用场景在哪儿？

猜想：分隔实例A与实例B的环境，不会存在挂载一个全局组件时，全局污染。但是具体如何实操多个应用实例呢？

# 侦听器
为什么双向数据绑定改为proxy之后，深度监听依旧还需要deep。是因为资源开销太大，所以故意限制了吗？

# css module
去scoped的区别，优势，$style用来干啥

# 组合式API与选项式API生命周期的结合方式
当选项式API中嵌入setup，那么在setup中定义的生命周期钩子，显式定义的钩子，例如onMounted会在和选项式API中与之对应的生命周期钩子结合，且先执行。

```
<script>
import { ref, onMounted } from 'vue'

export default {
  setup() {
    console.log('setup') 
    const count = ref(0)
    onMounted(()=>{
      count.value++
      console.log('onMounted:',count) 
    })
    // 暴露给模板和 API 钩子的其他选项
    return {
      count
    }
  },
  data(){
    return {
      test: 0,
    }
  },
  created(){
    console.log('created:',this.count) // 0
  },
  mounted() {
    console.log('mounted:',this.count) // 1
  }
}
</script>

<template>
  <button @click="count++">{{ count }}</button>
</template>

// 打印结果
setup
created: 0
onMounted: RefImpl {__v_isShallow: false, dep: Set(1), __v_isRef: true, _rawValue: 1, _value: 1}
mounted: 1
```

# 参考资料
[vue3中文官网](https://staging-cn.vuejs.org/)