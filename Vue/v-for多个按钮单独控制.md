## 问题描述

今天在开发使用 `v-for` 循环遍历服务器返回的数值时，业务需求如下：返回的数据过多，会隐藏一部分数据，但是会给用户提供一个 **“显示更多”** 的按钮，用于展开查看全部的信息。这个功能很容易实现，通过绑定 `data` 里的两个Boolean 值类型，然后按钮让这两个值取反就可以了。代码如下：

#### HTML 部分

```html
<div v-for="item in myData" :key="item.id" class="room-message">
  <div class="line">房间名称 ：{{ item.room }}</div>
  <div class="line">业主姓名 ：{{ item.name }}</div> 
  <!-- 显示更多里的信息 -->
  <transition name="plus-icon">
    <section class="more-message" v-if="showHidden">
      <div class="line">微信昵称 ：{{ item.wechat }}</div>
      <div class="line">ID ：{{ item.id }}</div> 
    </section>
  </transition> 
  <!-- 显示更多按钮以及隐藏按钮 -->
  <el-button type="primary" v-show="showMoreBtn" @click="changeBtn" class="show-btn" >
    显示更多
  </el-button>
  <el-button v-show="showHidden" @click="changeBtn" class="show-btn" >
    隐藏
  </el-button>
</div>
```

#### JavaScript 部分

```javascript
export default {
  components: {},
  data() {
    return {
      myData: [],
      showMoreBtn: true,
      showHidden: false,
    };
  }, 
  methods: { 
    changeBtn() {
      this.showMoreBtn = !this.showMoreBtn;
      this.showHidden = !this.showHidden;
    },
  },
};
```

#### 效果截图

![v-for多个按钮单独控制效果图1](https://cdn.jsdelivr.net/gh/shangguanyaa/NotesImages@main/img/v-for%E5%A4%9A%E4%B8%AA%E6%8C%89%E9%92%AE%E5%8D%95%E7%8B%AC%E6%8E%A7%E5%88%B6%E6%95%88%E6%9E%9C%E5%9B%BE1.png)

#### 方法 BUG

由于是绑定的都是同一个 `showMore` 和 `showHidden` ，所以如果返回的数据不止一个数组，`v-for` 循环出来了很多个按钮，都共用了这两个 `Boolean` 值去控制显示隐藏，所以这里要换一种思路。

## 解决方法：

### 方法一、

这里想到，可以利用每一个 `item` 的 `index` 值和获取的ID去进行判断相等，来控制 `v-if` 和 `v-show` 的显示隐藏，代码如下：

#### HTML

```html
<div v-for="(item, index) in myData" :key="item.id" class="room-message">
      <div class="line">房间名称 ：{{ item.room }}</div>
      <div class="line">业主姓名 ：{{ item.name }}</div> 
      <!-- 显示更多里的信息 -->
      <transition name="plus-icon">
        <section class="more-message" v-if="index == id">
          <div class="line">微信昵称 ：{{ item.wechat }}</div>
          <div class="line">ID ：{{ item.id }}</div> 
        </section>
      </transition> 
      <!-- 显示更多按钮以及隐藏按钮 -->
      <el-button type="primary" v-show="index != id" @click="changeBtn(item.id, index)" class="show-btn" >
        显示更多
      </el-button>
      <el-button v-show="index == id" @click="changeBtn(item.id, index)" class="show-btn" >
        隐藏
      </el-button>
    </div>
```

#### JavaScript

```javascript
export default { 
  data() {
    return {
      myData: [], 
      id: 3,
    };
  }, 
  methods: { 
    changeBtn(id, index) { 
      this.id = id - 1;
    },
  },
};
```

#### 方法一结论：

通过这样子，可以实现 `v-for` 循环的按钮，点击之后不会互相影响（个人感觉，其实本质上还是互相影响的，`v-if` 使用的都还是同一个 `id` 去判断 `true/false` ，也就又带来了一个新的问题：这样会有一个手风琴的效果（只能打开一个隐藏的盒子），如下效果图：

![解决方法一效果截图](https://cdn.jsdelivr.net/gh/shangguanyaa/NotesImages@main/img/%E8%A7%A3%E5%86%B3%E6%96%B9%E6%B3%95%E4%B8%80%E6%95%88%E6%9E%9C%E6%88%AA%E5%9B%BE.png)

- 总结
  - 会造成手风琴效果，标签只能有一个是true，也就是只有一个能打开。
  - 使用的是获取的 `item.id` 去进行控制，就必须要求有序的。
  - 其实也可以把 `item.id` 换成`index` ，就不用受到返回的 ID 影响，但是一样是手风琴效果。
  - 如果需求是使用手风琴效果，可以使用这个方法，缺的就是过渡而已了。

### 方法二、

可以在获取服务器返回的数据的时候，使用 `this.$set` ，去给每一项都加入一个 `isShow` 字段，值为 `false` ，这样就可以让 `v-if/v-show` 去基于这一个字段判断是否要显示。代码如下

#### HTML

```html
<div v-for="(item, index) in myData" :key="item.id" class="room-message">
      <div class="line">房间名称 ：{{ item.room }}</div>
      <div class="line">业主姓名 ：{{ item.name }}</div> 
      <!-- 显示更多里的信息 --> 
      <section class="more-message" v-if="item.isHidden">
        <div class="line">微信昵称 ：{{ item.wechat }}</div>
        <div class="line">ID ：{{ item.id }}</div> 
      </section> 
      <!-- 显示更多按钮以及隐藏按钮 -->
      <el-button type="primary" v-show="!item.isHidden" @click="showMore(item.id, index)" class="show-btn" >
        显示更多
      </el-button>
      <el-button v-show="item.isHidden" @click="Hidden(item.id, index)" class="show-btn" >
        隐藏
      </el-button>
    </div>
```

#### JavaScript

```javascript
export default { 
  data() {
    return {
      myData: [], 
    };
  }, 
  methods: { 
    showMore(id, index) {
      this.$set(this.myData[index], "isHidden", true);
    },
    Hidden(id, index) {
      this.$set(this.myData[index], "isHidden", false);
    },
  },
};
```

#### 效果图如下：

![解决方法二效果截图](https://cdn.jsdelivr.net/gh/shangguanyaa/NotesImages@main/img/%E8%A7%A3%E5%86%B3%E6%96%B9%E6%B3%95%E4%BA%8C%E6%95%88%E6%9E%9C%E6%88%AA%E5%9B%BE.png)

#### 使用的api 

```javascript
Array.map() // 方法返回一个新数组，数组中的元素为原始数组元素调用函数处理后的值。
Array.map() // 方法按照原始数组元素顺序依次处理元素。

this.$set(obj, key, value) // 为某一个对象或数组添加一个属性
```

