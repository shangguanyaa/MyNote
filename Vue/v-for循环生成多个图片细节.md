

## v-for 引入图片方法：

在今天的一眼映像官网页面开发中，首页使用了蚂蚁框架的走马灯（轮播图）控件，考虑到后期要让图片动态的从服务器获取，所以使用了`v-for` 去动态生成轮播图。在HTML结构里测试，使用 `v-bind` 直接对 `img 的 src` 属性动态绑定写入图片路径是没问题的。之后在 `Script` 脚本里data返回数据中，正常的写入图片路径的方法不起效果了。

查阅资料发现还有另外的一种引入方法，通过 `require` 引入，代码如下：

### JavaScript 部分

```javascript
data() {
    return {
      imgURL: [
        { img: require("../assets/images/index/index_bg01.jpg") },
        { img: require("../assets/images/index/index_bg02.jpg") },
        { img: require("../assets/images/index/index_bg03.jpg") },
        { img: require("../assets/images/index/index_bg04.jpg") },
      ],
    };
  }
```

### HTML 部分

```html
<a-carousel autoplay dots=false>
   <div v-for="(item, index) in imgURL" :key="index">
      <img :src="item.img" />
   </div>
</a-carousel>
```

通过 `require` 引入的方式，可以让 `v-for` 循环生成的 `img` 图片正常的显示。但是有个考虑，如果之后服务器返回的网路格式的图片URL，是不是不用通过`require`引入呢，因为`require`引入的是本地的图片路径。