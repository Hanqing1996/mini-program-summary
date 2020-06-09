#### app.json 
* 是当前小程序的全局配置，包括了小程序的所有页面路径、界面表现、网络超时时间、底部 tab 
* app.json 的 pages 字段可以知道你当前小程序的所有页面路径:
```
{
  "pages":[
    "pages/index/index",
    "pages/logs/logs"
  ]
}
```
* 小程序启动之后，在 app.js 定义的 App 实例的 onLaunch 回调会被执行:
```
App({
  onLaunch: function () {
    // 小程序启动之后 触发
  }
})
```

#### log.json 作用
* logs.json 配置生成一个界面，顶部的颜色和文字你都可以在这个 json 文件里边定义好。
* 每一个小程序页面也可以使用同名 .json 文件来对本页面的窗口表现进行配置，页面中配置项会覆盖 app.json 的 window 中相同的配置项。

#### rpx
* 开发小程序用的是 rpx
* rpx 转换成 px 是需要乘以一个系数的：
```
px = rpx * n
```
其中系数 n，是跟着设备改变的：
```
iPhone5: n = 2.34
iPhone6: n = 2
iPhone6 plus: n = 1.81
```
* 也就是说，我们写 10rpx,在 iPhone6 上表现出来的效果就是 20px
* 建议： 开发微信小程序时设计师可以用 iPhone6 作为视觉稿的标准。


#### 开发者工具预览时报错 iconPath="",file
> 这个 bug 的原因是 app.json 里面list的iconPath为空

#### 将 page 定义为 component
```
// json

{
  "component": true
}
```
```
// js
Component({

  data: {
    cards: [{}, {}, {}],
    selected: 0
  },
  methods: {
    updateSelected: function (event) {
      const currentIndex = event.target.dataset.index
      this.setData({
        selected: currentIndex
      });
  }
}
})
```
#### [数据监听](https://developers.weixin.qq.com/miniprogram/dev/framework/custom-component/observer.html)
> 通过 observers 实现。在一些数据字段被 setData 设置时，需要执行一些操作。
* computed
> this.data.sum 永远是 this.data.numberA 与 this.data.numberB 的和
```
Component({
  attached: function() {
    this.setData({
      numberA: 1,
      numberB: 2,
    })
  },
  observers: {
    'numberA, numberB': function(numberA, numberB) {
      // 在 numberA 或者 numberB 被设置时，执行这个函数
      this.setData({
        sum: numberA + numberB
      })
    }
  }
})
```
* watch
> 在 selected 更新后改变 content
```
  observers: {
    'selected': function (selected) {
      this.setData({
        content: this.data.cards[selected].content
      })
    }
  },
```

#### 微信小程序“是否授权XXX"的询问弹窗必须绑定按钮，不能用js唤起

#### [微信小程序登录流程](https://developers.weixin.qq.com/ebook?action=get_post_info&token=935589521&volumn=1&lang=zh_CN&book=miniprogram&docid=000cc48f96c5989b0086ddc7e56c0a#_ftn5)
[参考](https://juejin.im/post/5c335a4ee51d45527201668f)
