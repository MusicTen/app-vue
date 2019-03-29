# app-vue

### Project setup
```
npm install
```

### Compiles and hot-reloads for development
```
npm run serve
```

### Compiles and minifies for production
```
npm run build
```

------

### 问题汇总：

#### 1.子组件图片路径问题

​	首先图片路径 imgPath 有二种写法： 
​	一，如果图片是在 assets 文件夹下面的话，需要在路径前面加上 require 函数才行，否则 webpack 会识别不了路径报错

```javascript
"imgPath": require("../../assets/logo.png")，
```
​	二，还可以把图片放到 static 文件夹下面，这样的话就不需要 require ，可以像原来传统的方式那样写,
```javascript
"imgPath": "../../../static/logo.png"，
```

#### 2.路由跳转的三种方式

- <router-link to='需要跳转到的页面的路径> 浏览器在解析时，将它解析成一个类似于`<a>` 的标签。
- this.$router.push({ path:’/user’})
- this.$router.replace{path：‘/’ }

#### 3.\$route和$router的区别

- `$router` 为 `VueRouter` 实例，想要导航到不同 `URL`，则使用 `$router.push` 方法

- `$route` 为当前 `router` 跳转对象里面可以获取 `name` 、 `path` 、 `query` 、 `params` 等

#### 4.NextTick 是做什么的

​	`$nextTick` 是在下次 `DOM` 更新循环结束之后执行延迟回调，在修改数据之后使用 `$nextTick`，则可以在回调中获取更新后的 `DOM`

#### 5.Vue 组件 data 为什么必须是函数

​	因为js本身的特性带来的，如果 `data` 是一个对象，那么由于对象本身属于引用类型，当我们修改其中的一个属性时，会影响到所有Vue实例的数据。如果将 `data` 作为一个函数返回一个对象，那么每一个实例的 `data` 属性都是独立的，不会相互影响了

#### 6.深拷贝与浅拷贝

- 浅拷贝

  浅拷贝只复制一层对象的属性，并不包括对象里面的为引用类型的数据。

  对于数组，更长见的浅拷贝方法便是`slice(0)`和 `concat()`
  ES6 比较常见的浅拷贝方法便是 `Object.assign`

- 深拷贝

1. JSON.parse(JSON.stringify(obj))

   但是 `JSON.parse()`、`JSON.stringify`也存在一个问题，`JSON.parse() `和J `SON.stringify()`能正确处理的对象只有`Number、String、Array`等能够被 json 表示的数据结构，因此函数这种不能被 json 表示的类型将不能被正确处理。

2. 遍历实现属性复制

   既然浅拷贝只能实现非`object`第一层属性的复制，那么遇到`object`只需要通过递归实现浅拷贝其中内部的属性即可：

   ```javascript
   function extend (source) {
     var target
     if (typeof source === 'object') {
       target = Array.isArray(source) ? [] : {}
       for (var key in source) {
         if (source.hasOwnProperty(key)) {
           if (typeof source[key] !== 'object') {
             target[key] = source[key]
           } else {
             target[key] = extend(source[key])
           }
         }
       }
     } else {
       target = source
     }
     return target
   }
   ```

#### 7.vue自定义轮播组件

​	难点在于给每个图片的切换过程添加过渡效果。可尝试使用Vue自带的class钩子，或直接使用css的transition属性，也可以用`setTimeout`方法加递归来实现。如果使用css的`transition`过渡方法，在处理边界的无限滚动上总会在chrome浏览器上有一下闪动，即使添加了`-webkit-transform-style: preserve-3d;`

 和` -webkit-backface-visibility: hidden;` 也还是没用，而且要配合`transition`的 `transitionend `事件对于IE浏览器的支持也不怎么好。

​	实现思路：首尾各加一张假图，利用css3过渡属性，由于vue中我没法监听过渡完成`transitionend`事件，取巧用延迟来控制图片位置秒切，并设置节流阀。

#### 8.vue 引入公共css文件

​	1、在入口js文件main.js中引入，一些公共的样式文件，可以在这里引入。

```html
import Vue from 'vue'
import App from './App' // 引入App这个组件
import router from './router' /* 引入路由配置 */
import '../static/css/global.css' /*引入公共样式*/
```

​	2、在index.html中引入

```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8">
    <title>stop</title>
    <link rel="stylesheet" href="./static/css/global.css"> /*引入公共样式*/
  </head>
  <body>
    <div id="app"></div>
    <!-- built files will be auto injected -->
  </body>
</html>
```


​	3、在app.vue中引入，但是这样引入有一个问题，就是在index.html的HEADH上会多出一个空的`<style></style>`

```html
<template>
  <div id="app">
    <router-view/>
  </div>
</template>

<script>
export default {
  name: 'app'
}
</script>

<style>
  @import './../static/css/global.css'; /*引入公共样式*/
</style>
```

#### 9.`<transition-group>` 组件

- 不同于 `<transition>`，它会以一个真实元素呈现：默认为一个 `<span>`。你也可以通过 `tag` 特性更换为其他元素。
- [过渡模式]不可用，因为我们不再相互切换特有的元素。
- 内部元素 **总是需要** 提供唯一的 `key` 属性值。