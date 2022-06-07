- 注意一下内置的异步请求方法的好处？有没有必要替换 axios？
- nuxt3 是全 SSR 渲染模式，思考全 SSR 模式的优劣
- 注意下 components 与 composables 目录的区别

  答案：可以参考 vue3 文档：[组合式函数](https://staging-cn.vuejs.org/guide/reusability/composables.html#what-is-a-composable)

- NUXT3 和 vue3 有一个区别，vue3 的 template 已经不需要单个根节点了，但是 nuxt3 还是需要，否则会影响路由转换
- APP.vue 文件，为什么比 Nuxt2 多了一个主文件呢？

# 技术栈变化

- `Nuxt3` 的主要变化，是基于 `Vue3` 重构了，融入了 `composition API`，双向数据绑定等核心内容的重构。所以 使用 `Nuxt3` 的前置条件是先熟悉 `Vue3` 。
- 由于整个 `Vue` 生态体系的逐步完善壮大， `vite` 被孵化出来以后，也在整个体系中强力推广，虽然个人感觉目前还不能完全取代 `webpack`，但是既然已经采用 `Vue` 体系，那还是有必要使用一下的
- `Pinia` 看似取代了 `Vuex` ， 但实际上可以理解成，它就是最新版本的 `Vuex`，更方便，有必要上手
- `Vue3` 就是使用 `TS` 重构的，对于 `TS` 的支持很棒，从`Vue` 团队的可以看出，这是一个大趋势，我们没有理由不跟上，而且团队现在处于多人开发的的状态，使用 `TS` 会提高开发的安全性与可维护性。

所以我们我们的技术栈会从

> vue2 + vuex + webpack

转变成为

> vue3 + Pinia + vite
