## 背景

今天在项目的练习中，做的是模拟 `tabbar` ，需求是单击活跃的哪一个 `item` ，哪一个 `item` 的样式就发生改变，这样能更清晰的看到现在正在处于哪一个页面。当下是通过动态绑定类名 `class` 来实现的，下面是 `HTML` 代码：

### HTML部分

使用了 `vue` 组件化使用的插槽 `slot` ，去保证 `tabbar` 里的每一个item 里的内容不是写死的，而需要使用类似于动态绑定类名的需求，一般会在插槽外多加一个元素，如 div 等，把动态绑定的属性值写在这个父级元素上，这样在传入内容到 `slot` 插槽后，需要的属性就会继续从父级元素继承下来。

```javascript
<template>
  <div class="tabbar-item" @click="itemClick">
     <div :class="{active: isActive}">
      <slot name="item-text"></slot>
    </div>
  </div>
</template>
```

插槽父级页面的传值如下：

```javascript
<template>
  <div id="app">
    <router-view></router-view>
    <tab-bar>
      <tabbar-item path="/home">
        <img slot="item-icon" src="./assets/img/tabbar/01hangzhengguanli.svg">
        <div slot="item-text">Home</div>
      </tabbar-item>
      <tabbar-item path="/page">
        <img slot="item-icon"
             src="./assets/img/tabbar/02caiwuguanli.svg">
        <div slot="item-text">Page</div>
      </tabbar-item>
    </tab-bar>
  </div>
</template>
```

### JavaScript 部分

这里使用了计算属性去进行。本来可以考虑在 `data` 里面 `return` 里定义一个 isActive 变量去控制，但是这样子达不到点击 `item` 的时候全部控制。如果在data 里定义的变量 `isActive=false`，我们可以在 item 的 `@click` 点击事件里面去让这个变量取反，但是这样子只能达到在点击 a页面 之后，a页面 自身发生改变，接下来点击其他页面，其他页面的 `item` 也会发生改变，但是 a页面 单击过了，即使已经不在活跃状态了，这个变量也还是变成了 true ，也就是 a页面 的 `item` 样式也还会保持在活动状态。

而使用 `computed` 计算属性，就可以让页面每次发生改变都会自己去判断进而改变自己的 item 的状态。

`computed` 计算属性也能够进行监听。

（ 这里有一个疑问，就是 `computed` 属性的触发机制是什么，为什么可以监听到，留着以后解决了再补充 ）

```javascript
export default {
  props: { path: String },
  computed: {
    isActive() {
      return this.$route.path.indexOf(this.path) !== -1
      // indexOf 方法：返回某个指定的字符串值在字符串中首次出现的位置
      // 如果字符串没出现过，则返回 -1
    }
  },
}
```

## 实现理解

通过 `this.$route.path.indexOf(this.path) !== -1` 去实现

`this.$route` 就是取到当前活跃的 `router` 路由，点击了这个 `item` 之后，当前活跃的 `router` 就会发生改变，从而 `$route` 对象里的东西也会发生改变。取出当前活跃的 `route` 对象里面的 `path` 去和接受到传过来的 `path` 去进行比较，如果这两个`path` 值是一样的，就说明这个 `route` 当前所在的页面对应的 `item` 是活跃的。

这里有一个思考，为什么不用字符串去判断相等： `this.$route.path == this.path` 

明白了：到项目后期，`route` 的 path 不只是单单的一层 `/home` ，有可能还有 `/web/home` ，而这时候即使确实打开的是这个页面，但是也不相等而返回 false 

所以考虑使用 `indexOf` 函数：

- indexOf 
  - indexOf 方法：返回某个指定的字符串值在字符串中首次出现的位置
  - 如果字符串没出现过，则返回 -1

只要不返回 -1 ，就说明现在打开的页面的 `path` 里包含了传过来的 `path` 值

下面是 `demo` ：

```js
<script type="text/javascript">

	var str="Hello world!"
	document.write(str.indexOf("Hello") + "<br />")
	document.write(str.indexOf("World") + "<br />")
	document.write(str.indexOf("world"))

</script>
```

输出如下：

```js
0
-1
6
```