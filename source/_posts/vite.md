---
title: vite
date: 2021-11-28 18:09:37
tags: 构建工具
---

# vite及旧项目改造思路

## 导读

本文主要围绕vite诞生背景、初衷、原理、为何使用、如何使用、如何在旧项目中使用，这几个问题来说，以达到对vite有一个全面的认识，并运用自如。

## 背景

vite诞生在2020年，这一年，[ECMAScript](https://zhuanlan.zhihu.com/p/367249029)发布了他的第十一个版本，原生模块化也已经诞生了5个年头。除ie浏览器之外，所有主流浏览器都已支持了该特性。市面上各种[构建工具](https://blog.csdn.net/qq_36538012/article/details/110425213)百花齐放，webpack也迎来了它的巅峰时刻。

就是在这样一个背景之下，vite横空杀出，并迅速获取了相当高的热度。我认为原因有一下几点：

- 归功于vue的大火，使得尤大自带热度。
- 诟病与webpack在大型项目本地开发中速度低下，使得市场急需一种解决方案。

## 初衷

webpack等构建工具是时代的产物，它使前端开发者们更加高效，和有条理的管理我们的项目，并且优雅的使用各种框架，不用过多考虑新语法的兼容性，通过插件系统高效的处理一系列同等问题等。对于高速发展的前端来说，具有举足轻重的地位。

然而面对大型应用，需要处理的文件和代码量很多，这时传统的构建方式开始出现弊端，当我们启动一个前端项目时，需要等待数分钟或者数十分钟来等待，即使使用热更新也需要数秒。这对于一个追求极致效率的前端开发者来说是不能忍受的。因此，vite诞生了，vite旨在利用生态系统中的新进展来解决上述问题：即ESM

## 原理

这里有一个问题，为什么vite比传统构建方式快？

这里使用webpack和vite进行对比。

### webpack简述

首先对于[webpack的原理](https://zhuanlan.zhihu.com/p/363928061)进行简单的了解。

webpack目前已经出到了5.0版本。具有模块打包、代码分割、按需加载、HMR、Tree-shaking、文件监听、sourcemap、Module Federation、devServer、DLL、多进程等等功能，非常复杂，也非常强大（这也是为什么会有webpack工程师的原因，并且短时间内不能被取代）。

webpack构建过程可以简单理解为，通过入口文件中的require和import递归解析出整个项目的文件依赖关系，然后进行整合出bundler文件的过程。

期间会使用各种loader将不同类型的文件解析为AST

在构建过程中或者递归AST时触发相应的插件增强功能

### vite原理

通过[vite中文网](https://cn.vitejs.dev/guide/why.html#slow-server-start)所述，Vite 通过在一开始将应用中的模块区分为 依赖 和 源码 两类，改进了开发服务器启动时间。

- 依赖 大多为在开发时不会变动的纯 JavaScript。一些较大的依赖（例如有上百个模块的组件库）处理的代价也很高。依赖也通常会存在多种模块化格式（例如 ESM 或者 CommonJS）。

- Vite 将会使用 esbuild 预构建依赖。Esbuild 使用 Go 编写，并且比以 JavaScript 编写的打包器预构建依赖快 10-100 倍。

- 源码 通常包含一些并非直接是 JavaScript 的文件，需要转换（例如 JSX，CSS 或者 Vue/Svelte 组件），时常会被编辑。同时，并不是所有的源码都需要同时被加载（例如基于路由拆分的代码模块）。

由此可以得出vite具有以下特点
- 使用ESM来处理模块之间的关系
- 去掉打包步骤
- 实现按需加载


## 为何使用

作为一名业务开发人员，事件无疑是最宝贵的，因此如何利用好各种工具来提高开发效率显得十分重要。

但是经常受长时间的装包，构建过程所累，寻找一种高效的本地开发工具，还有更加快速npm包拉去方案迫在眉睫。

vite相较于webpack的静态打包方式，不用关心各个模块之间的关系，只需要将不同的文件解析成浏览器可以识别的语言即可。因此构建时间更短，而且esbuild使用Go语言编写，相比使用js的webpack，构建速度也有提升。

通过以上对比和需求，vite是值得一用的

## 如何使用

vite的使用非常简单

首先我们需要一个支持ESM的浏览器，装一个node开发环境，一个需要使用vite的项目。

项目中需要一个index.html文件用来作为入口文件，另外需要一个vite.config.js文件作为vite的配置文件


## 如何使用
对于一个简单项目来说，上面的配置已经可以使用vite愉快的开发了

但是现实是残酷的，我们需要维护相当多的旧项目，并且实际项目当中也并未想象中的简单，因此需要配置，以及插件来帮助我们使用vite。

1、对于一个需要使用react热重载的项目来说，需要引入`@vitejs/plugin-react`插件


安装
```
npm install @vitejs/plugin-react --save
```

使用
```
import { defineConfig } from 'vite'
import react from '@vitejs/plugin-react'

export default defineConfig({
  plugins: [react()]
})
```

2、vite直接支持ts，因此不需要额外的配置
3、vite对于vue支持的很好，有官方插件可以使用
4、jsx语法vite也是直接支持的，也不需要额外配置
5、vite直接支持css module，内置了sass，scss，lessd等的处理方式，但需要安装包
```
# .scss and .sass
npm install -D sass

# .less
npm install -D less

# .styl and .stylus
npm install -D stylus
```
6、对于一些静态文件或者json，vite也是直接支持的。

vite可以说是开箱即用的，帮我们内置了很多开发中常见的工具

这里说一下需要配置的属性

1、base的配置。为了通过路由转发，和前后端同构的需要，base十分常见。
2、全局变量的定义，有时开发环境需要用到一些全局变量，可以通过define属性定义
```
// vite.config.js文件中导出
export default {
  define: {
    __CLIENT__: true,
    __DEVELOPMENT__: true,
    __DEVTOOLS__: true,
    __SERVER__: false,
  },
}
```
3、别名的定义，在使用webpack的时候，有时为了引入方便，通常会使用别名，
vite也是支持的
```
export default {
  resolve: {
    alias: {
      utils: path.resolve(__dirname, './client/utils'),
      components: path.resolve(__dirname, './client/components'),
      modules: path.resolve(__dirname, './client/modules'),
      styles: path.resolve(__dirname, './client/styles'),
      config: path.resolve(__dirname, './client/config'),
    },
  },
}
```

此外还有许多有趣的配置，用来兼容老项目。

但配置再多，终究是有漏网之鱼

比如 

对于使用外链引入的js文件，在window中绑定了变量，但使用模块化的方式引入，webpack可以通过externals属性进行配置，但是vite不支持该属性，因此需要插件来处理

安装
```
npm i --save vite-plugin-externals
```
使用
```
export default {
    plugins: [
        viteExternalsPlugin({
            '@iyunbao/components': '__iybComponents',
            '@iyunbao/utils': '__utils',
        }),
    ]
}

```


在比如预构建虽然帮我们解决了npm包中的commonjs等模块化的问题，
但是项目中的依然会有require，module等共存的情况，虽然可以使用optimizeDeps.include强制进行构建，但是如此重复的劳动显然不符合我们的开发理念，人和动物区别也在此，人可以使用工具来提高生产效率。

这里我们依然使用插件来处理这个问题

旧的项目中碰到的同类问题基本都可以使用插件来做

这里以mudule，require为例写一个cjs转ESM的插件

首先需要定义一个函数放在plugins中并执行
```
export default {
    plugins: [
        ((options) => {
            return {
                name: 'vite-pulgin-cjs2esm',
                transform(code, id) {
                return null;
                }
            };
        })({}),
  ],
}
```

这个函数返回一个对象，包含一个插件名称，还有一个transform属性，是一个函数，
