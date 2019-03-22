# 引言
本文档并非概念或者理论的前端工程化介绍，而是工程实战，所以文档中用到的很多技术/工具不是一定而是可替换。

[Vue](https://cn.vuejs.org/) 是一套构建用户界面的渐进式框架。只关注视图层， 采用自底向上增量开发的设计。

[Vue CLI](https://cli.vuejs.org/guide/)  是一个基于 Vue.js 进行快速开发的脚手架

[vue-router](https://router.vuejs.org/zh/)  Vue.js 官方的路由管理器。它和 Vue.js 的核心深度集成，让构建单页面应用变得易如反掌。

[Vuex](https://vuex.vuejs.org/zh/) 是一个专为 Vue.js 应用程序开发的状态管理模式。它采用集中式存储管理应用的所有组件的状态，并以相应的规则保证状态以一种可预测的方式发生变化。

[vue-ssr](https://ssr.vuejs.org/zh/) Vue.js 是构建客户端应用程序的框架。默认情况下，可以在浏览器中输出 Vue 组件，进行生成 DOM 和操作 DOM。然而，也可以将同一个组件渲染为服务器端的 HTML 字符串，将它们直接发送到浏览器，最后将静态标记"混合"为客户端上完全交互的应用程序。服务器渲染的 Vue.js 应用程序也可以被认为是"同构"或"通用"，因为应用程序的大部分代码都可以在服务器和客户端上运行。

[axios](https://www.npmjs.com/package/axios) 基于 promise 的 HTTP 库

[webpack](https://www.webpackjs.com/concepts/) 是一个现代 JavaScript 应用程序的静态模块打包器(module bundler)。当 webpack 处理应用程序时，它会递归地构建一个依赖关系图(dependency graph)，其中包含应用程序需要的每个模块，然后将所有这些模块打包成一个或多个 bundle。

[ESLint](https://eslint.org/) 是一个JavaScript语法规则和代码风格的检查工具，可以用来保证写出语法正确、风格统一的代码。

# 使用vue-cli脚手架创建项目
- 安装node.js
- 安装webpack
```
npm install webpack webpack-cli -g
```

- 安装vue-cli
```
npm install --global vue-cli
```

- 安装完成验证
```
vue -V
```

- 使用webpack模板创建项目
```
vue init webpack projectname
然后输入项目名，作者，项目配置等信息
```

- 安装项目依赖的库
```
npm install
```

- 编译运行
```
npm run dev
```

- 验证
```
浏览器打开http://localhost:8080/#/
```


参考：
- http://vuejs-templates.github.io/webpack/
- https://blog.csdn.net/u014182411/article/details/72596722
- https://github.com/varHarrie/varharrie.github.io/issues/7
 
# 进阶
## 添加sass loader
[Sass](https://www.sass.hk/docs/) 是一款强化 CSS 的辅助工具，它在 CSS 语法的基础上增加了变量 (variables)、嵌套 (nested rules)、混合 (mixins)、导入 (inline imports) 等高级功能，这些拓展令 CSS 更加强大与优雅。使用 Sass 以及 Sass 的样式库（如 Compass）有助于更好地组织管理样式文件，以及更高效地开发项目。
  
```
npm install sass-loader node-sass --save-dev
```

## element
npm安装
```
npm i element-ui -S
```
```
npm install element-ui --add
```
修改main.js
```
import ElementUI from 'element-ui'
import 'element-ui/lib/theme-chalk/index.css'
Vue.use(ElementUI)
```


## axios 
Axios 是一个基于 promise 的 HTTP 库,可以用在浏览器和 node.js 中。

安装
```
npm install axios
```

# 开工
## 添加状态管理vuex
安装
```
npm install vuex --save
```
创建/src/store目录，新建index.vue文件
```
import Vue from 'vue'
import Vuex from 'vuex'

Vue.use(Vuex)

const store = new Vuex.Store({
  modules: {
  }
})

```
根据需求编写相应vuex模块，通过modules对象传入Store。

在main.js中引入store 将状态从根组件“注入”到每一个子组件中（需调用 Vue.use(Vuex)）
```
import store from './store'
new Vue({
  ...
  store,
  ...
})
```

## 简单的登录页

创建/src/views/login目录，新建index.vue文件
```
<template>
  <div class="login-container">
    <el-form :model="loginForm" status-icon :rules="rules" ref="loginForm" label-width="100px" class="login-form">
      <el-form-item label="账号" prop="account">
        <el-input v-model="loginForm.account" autocomplete="off"></el-input>
      </el-form-item>
      <el-form-item label="密码" prop="password">
        <el-input type="password" v-model="loginForm.password" autocomplete="off"></el-input>
      </el-form-item>
      <el-form-item>
        <el-button type="primary" @click="submitForm('loginForm')" v-loading.fullscreen.lock="fullscreenLoading">登录</el-button>
        <el-button @click="resetForm('loginForm')">重置</el-button>
      </el-form-item>
    </el-form>
  </div>
</template>
<script>
export default {
  data() {
    var validatePassword = (rule, value, callback) => {
      if (value === "") {
        callback(new Error("请输入密码"))
      } else {
        callback()
      }
    }
    var validateAccount = (rule, value, callback) => {
      if (value === "") {
        callback(new Error("请输入账号"))
      }
      {
        callback()
      }
    }
    return {
      loginForm: {
        account: "",
        password: ""
      },
      fullscreenLoading: false,
      rules: {
        account: [{ validator: validateAccount, trigger: "blur" }],
        password: [{ validator: validatePassword, trigger: "blur" }]
      }
    }
  },
  methods: {
    submitForm(formName) {
      this.$refs[formName].validate(valid => {
        if (valid) {
          this.fullscreenLoading = true
          setTimeout(() => {
            this.$message(
              "登录成功" +
                this.loginForm.account +
                " " +
                this.loginForm.password
            )
            this.fullscreenLoading = false
            setTimeout(() => {
              this.$router.push({ path: "/dashboard" })
            }, 500)
          }, 500)
        } else {
          console.log("error submit!!")
          return false
        }
      })
    },
    resetForm(formName) {
      this.$refs[formName].resetFields()
    }
  }
}
</script>
```

在/src/router/index.js添加路由
```
{
    path: '/login',
    component: () => import('@/views/login')
}
```

## 登录进阶————验证登录状态
修改router当发现未登录的时候则主动跳转到登录页
```
router.beforeEach((to, from, next) => {
  var userInfo = JSON.parse(sessionStorage.getItem('userInfo')) // 获取浏览器缓存的用户信息, 这只是一个示例具体实现会不同
  if (userInfo) { // 如果有就直接到首页咯
    next()
  } else {
    if (to.path === '/login') { // 如果是登录页面路径，就直接next()
      next()
    } else { // 不然就跳转到登录；
      next('/login')
    }
  }
})
```
同时修改login的逻辑使得能够拿到并记录userInfo

## 自动注册全局组件
我们默认components目录下的组件为全局组件
[reference](https://cn.vuejs.org/v2/guide/components-registration.html#%E5%9F%BA%E7%A1%80%E7%BB%84%E4%BB%B6%E7%9A%84%E8%87%AA%E5%8A%A8%E5%8C%96%E5%85%A8%E5%B1%80%E6%B3%A8%E5%86%8C)

有了它就可以安心撸组件了

## 添加mock
安装mock
```
npm install mockjs --save-dev
```

创建/src/mock目录，增加mock.js.

引入库
```
import Mock from 'mockjs'
```
配置mock
```
var mockFunc = function(){
  return {}
}
Mock.mock(/\/login\/login/, 'post', mockFunc)
```
在main.js中引入
```
import './mock'
```
