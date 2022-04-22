<script>
import { ref, onMounted } from 'vue'

export default {
  setup() {
    const count = ref(0)
    onMounted(()=>{
      count.value++
    })
    // 暴露给模板和 API 钩子的其他选项
    return {
      count
    }
  },
  created(){
    console.log(this.count) // ?
  },
  mounted() {
    console.log(this.count) // ?
  }
}
</script>

<template>
  <button @click="count++">{{ count }}</button>
</template>