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

#### 获取节点高度
> 在 component 中，必须放在 pageLifetimes 的 show 方法中
```
wx.createSelectorQuery().selectAll('.page').boundingClientRect(function (rect) {
  console.log(rect[0].height)
  }).exec()  
```

#### 页面跳转
[参考](https://blog.csdn.net/qq_26585943/article/details/54584558)
1.wx.navigateTo(OBJECT)
2.wx.redirectTo(OBJECT)
3.wx.switchTab(OBJECT)
4.wx.navigateBack(OBJECT)
```
navigateTo是将原来的页面保存在页面栈中，在跳入到下一个页面的时候目标页面也进栈，只有在这个情况下点击手机的返回按钮才可以跳转到上一个页面；
redirectTo和switchTab都是先清除栈中原来的页面，然后目标页面进栈，使用这两种跳转方式，都不能通过系统的返回键回到上一个页面，而是直接退出小程序；
redirectTo使用的时候一定要配合tabBar或是页面里面可以再次跳转按钮，否则无法回到上一个页面；
switchTab跳转的页面必须是tabBar中声明的页面；
tabBar中定义的字段不能超过5个页面，小程序的页面栈层次也不能超过5层。
navigateBack只能返回到页面栈中的指定页面，一般和navigateTo配合使用。
wx.navigateTo 和 wx.redirectTo 不允许跳转到 tabbar 页面，只能用 wx.switchTab 跳转到 tabbar 页面
```

#### 取消微信小程序自带导航栏
> 在app.json window 增加 navigationStyle:custom ，顶部导航栏就会消失
