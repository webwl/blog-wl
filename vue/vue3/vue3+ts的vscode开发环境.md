# 安装相关vscode插件
- Vue3 Snippets
- Volar
- 禁用掉Vetur

# 类型声明
安装vue3+ts的模板，一进去可能会报错
> 找不到模块“xx”或其相应的类型声明

需要安装 Node.js 的声明文件，npm i @types/node -D

# 配置风格校验
推荐参考资料中的《掘金01-脚手架开发》配置就行

# 报错解决
> 'rules' has been removed. Please use the 'overrideConfig.rules' option instead.

可能会遇到类似的报错，是因为你本地的`vscode`的`settings.json`配置有点老了，需要替换成新的格式
```
// 旧的
"eslint.options": {
  "rules": {
      "indent": ["error", 2, { "SwitchCase": 1 }],
  }
},

// 新的
"eslint.options": {
  "overrideConfig":{
    "rules": {
      "indent": ["error", 2, { "SwitchCase": 1 }],
    }
  }
},
```
需要用`overrideConfig`包裹起来

# 参考资料
- [掘金01-脚手架开发](https://juejin.cn/post/7036745610954801166#heading-8)