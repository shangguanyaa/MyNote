## 简介

在这两天的开发中，我自己选了一下自己先要精通的方向，决定先把中心放在前端。在2月2号的晚上下班前十几分钟，公司领导李玉齐找我单独去公司8楼聊天，始末是这样的。老乡领导叶志伟和我说，可以帮问问领导，能不能给我提薪，估计就是问过了之后，领导找我聊天。

上去之后，显示了解了一下我大概的技术栈什么的，然后就给我灌输什么，不太记得了，意思就是他问集团那边，工资是不能提的，实习生。但是如果我转正之后，他能保证可以给我两倍多的工资。

现在想想还是估计就是画饼，当天应该强硬点，如果不提薪，年后就不来了。

回归正传，这两天都在继续`vue.js`的一个学习，继续跟着看视频之后，我对`JS`的封装引用等有了一点点的了解，于是，今天的需求如下:

### 需求

在avue的表格里，在添加编辑操作的时候，有几个是需要以下拉框的形式展现，而且数据来源是根据其他的接口获取到的。这个就像上课的那个动态下拉框以及我们比赛离港前端软件的选择机场软件版本等等的动态下拉框。

框架本来带有自带的很好用的一个封装，在表格的 `option` 的`js`里，可以直接的对表格结构进行设置，在表格结构中，还有很多的类型可以选择，比如下图，通过设置`prop`绑定数据库返回的需要渲染的字段，label去绑定该字段显示的表头，而 `type` 则表示这个东西再编辑和添加的时候是什么类型的展示，设置`select` 就是下拉框，`radio`就是单选框等等，很好用。

![image-20210204154434527](.\images\JS初次尝试高级用法\image-20210204154434527.png)

甚至可以直接把要请求的URL写在这里面，告诉框架这是一个需要请求的下拉框，然后绑定字段之后，就能直接的渲染到页面上，无论是包含了多少层的 `data.data.data...` 这个功能就很强大。

由于个人的粗心大意，在给绑定字段后的props写错了，所以页面上是空白的下拉框，然后我以为是框架或者接口的问题，就去折腾的自己写`JS`代码。

```javascript
import {
  pagequery
} from "@/api/chargeManagement/chargeFinancial"
const newData = []
const relationFinanceName = {}
let newObj = []

pagequery().then((res) => {
  // debugger
  let data = res.data.data.records
  data.forEach((item) => {
    newData.push(item.itemName)
  });
  for (let key in newData) {
    relationFinanceName[key] = newData[key];
  };
  newObj = Object.keys(relationFinanceName).map(val => ({
    label: relationFinanceName[val],
    value: relationFinanceName[val]
  }));
  console.log(newObj, '//////////////////////');
  console.log(newData, '----------++++----------');
})

{
      prop: "chargeType",
      type: "select",
      label: "计费类型",
      labelWidth: "30%",
      dicData: newObj,
},
```

