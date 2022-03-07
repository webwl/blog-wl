# 什么是nuxt
基于vue的SSR框架

# 目录与路由
nuxt有严格的约定好的基础目录，如assets,layouts等，不可重命名，需要按官方的来。

nuxt会扫描名为pages的页面目录，读取框架下所有`.vue`文件，自动生成对应的路由配置

具体有哪些固定目录，路由具体如何生成，自己查看官方文档

# 生命周期
因为nuxt是服务端渲染，所以生命周期要分为服务端生命周期与客户端生命周期。其中`beforCreate()`和`created()`是服务端客户端都需要执行的。

## 服务端
- nuxtServerInit 服务端初始化
- middleware 中间件运行
- validate() 校验参数
- asyncData() & fetch() 异步数据处理
- Render 客户端开始渲染
- beforeCreate(),created()

## 客户端
- beforeCreate(),created()
- vue其他生命周期

## 详解
### nuxtServerInit
nuxtServerInit可以卸载store中，它可以在服务端启动时执行，也就是你的`npm run dev`执行成功以后。

### middleware中间件
> 中间件允许我们定义一个自定义函数运行在一个页面或一组页面渲染之前。每一个中间件应放置在 middleware/ 目录。文件名的名称将成为中间件名称 (middleware/auth.js将成为 auth 中间件)。

中间件接收上下文对象，我们可以对当前上下文对象做一定处理。

中间有不同级别的，就像vue的路由一样。所以不同级别的中间件有自己的执行顺序

> nuxt.config.js中配置的 -> 匹配layouts -> 匹配pages

具体配置方法查看官网

### validate
validate是nuxt新增的一个生命周期，它可以在组件内配置。validate可以在组件路由发生变化的时候，做参数校验。返回一个布尔值，如果返回false，则客户端无法访问这个页面。

### asyncData() & fetch()
#### asyncData
因为nuxt需要在服务端就完成首屏的内容渲染，直接返回给客户端一个html页面，所以需要在首屏加载的内容，都需要在这两个生命周期中去请求。

但是需要注意的是，我们都知道 async/await 会将异步任务去同步化执行，上一个异步任务没结束之前，下一个异步任务处于等待状态中。这样需要等待3个异步任务，假设这些请求均耗时1秒，也就是说页面至少要等待3秒后才会出现内容。原本我们想利用服务端渲染来优化首屏，现在却因为等待请求而拖慢页面渲染。所以最好使用`Promise.all`来批量异步操作。

```
asyncData(context){
  // 数组解构获得对应请求的数据
    let [indexData, recommendAuthors, recommendBooks] = await Promise.all([
      app.$api.getIndexList({
        first: 20,
        order: 'POPULAR',
        category: 1
      }).then(res => res.s == 1 ? res.d : {}),
      app.$api.getRecommendAuthor({ 
        limit: 5
      }).then(res => res.s == 1 ? res.d : []),
      app.$api.getRecommendBook().then(res => res.s === 1 ? res.d.data : []),
    ])
    return {
      indexData,
      recommendAuthors,
      recommendBooks
    }
  }
}
```
在asyncData中return出来的内容，可以当做是data对象中return出来的内容，直接通过this获取或者在template中使用。

#### fetch
fetch方法专门用来在渲染页面前填充Vuex数据
```
export default {
  async fetch ({ store, params }) {
    let { data } = await axios.get('http://my-api/stars')
    store.commit('setStars', data)
  }
}

```
虽然fetch方法是阻焊们用来填充Vuex数据的，但这并不是说我们只能在 fetch 中填充状态树，在 asyncData 中同样可以。

# 持久化
本地持久化需要改成cookie,在服务端的生命周期中无法操作浏览器的存储空间。例如登录token，一般需要存储在本地的。

# 参考资料
- [nuxt中文网](https://www.nuxtjs.cn/guide)
- [实践-掘金](https://juejin.cn/post/6844904160324747278)

