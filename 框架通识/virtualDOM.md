# 什么是Virtual Dom
通过js模拟实现DOM树,或者说通过js，描述DOM树长什么样子

例如：
真实的DOM树长这样
```
<div class="haha">
  哈哈哈哈
  <p class="hehe">呵呵呵呵</p>
</div>
```
虚拟的DOM树
```
{
  tagName: 'div',
  props: {
    class: 'haha'
  },
  text: '哈哈哈哈',
  children:[
    {
      tagName: 'p',
      props: {
        class: 'hehe'
      },
      text: '呵呵呵呵',
    }
  ]
}
```
当有一个一颗完整的虚拟DOM树时，通过
- `createElement`
  用于创建一个由标签名称 tagName 指定的 HTML 元素
- `setAttribute`
  设置指定元素上的某个属性值。如果属性已经存在，则更新该值
- `createTextNode`
  创建一个新的文本节点
- `appendChild`
   将一个节点附加到指定父节点的子节点列表的末尾处

等方法，去绘制一颗真实DOM树

# Virtual Dom的好处
要想明白Virtual Dom的好处先看浏览器渲染的基本步骤
- 处理 HTML 并构建 DOM 树。
- 处理 CSS 构建 CSSOM 树。
- 将 DOM 与 CSSOM 合并成一个渲染树。
- 根据渲染树来布局，计算每个节点的位置。
- 调用 GPU 绘制，合成图层，显示在屏幕上。

在传统开发模式中，直接输入用js操作真实DOM，每一次操作DOM，浏览器都会执行一遍上述流程。假如某一次操作中，我们更新了10个DOM节点，浏览器在接受到第一个DOM修改时，并不知道之后还有几次操作，因此会立刻执行流程。一次操作却执行了10次浏览器渲染，这显然是显然是十分浪费性能的，地主家也没有余粮啊！（狗头）

```
// alldom 已经通过js创建好的节点
document.body.appendChild(alldom); 
```
通过递归循环，创建dom节点，最后再添加到页面的body上面，实现多节点修改，一次渲染

Virtual Dom完美的解决了浏览器无法识别在一次操作中更新多个节点的问题。每一次操作节点并没有直接通知浏览器，而是渲染到虚拟DOM树中，等所有操作绘制完成，再一次性通知浏览器执行渲染。那么对于浏览器来讲，就只收到了一次渲染，极大的节约了性能。

# 算法实现
要在庞大的dom树中，去识别哪些部分被改动了，这需要一套优秀的算法来实现。Virtual Dom通过diff算法来实现。


# 参考资料
  - [深入剖析：Vue核心之虚拟DOM](https://juejin.cn/post/6844903895467032589)
  - [前端面试之道-virtual-dom](http://caibaojian.com/interview-map/frontend/framework.html#virtual-dom)