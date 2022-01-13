# 如何发布一个自己的npm包？
## 步骤1
拥有一个自己的npm账号

登录`https://www.npmjs.com/`,注册一个自己的账号

## 步骤2
随便`npm init`一个项目准备用来测试发版

注意取一个私密一点的，复杂一点的名字不要污染npm池子

## 步骤3

在本地登录你的npm账号
> npm adduser

根据提示，按步骤输入账号，密码，邮箱

最后你可能会遇到报错，注意检查你的npm源是否有问题，使用taobao的源会报错，切换回npm官方源

> npm config set registry https://registry.npmjs.org/

## 步骤4
> npm publish

提交

# 如何写一个有效的包？