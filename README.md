[参考](https://juejin.im/post/5b0ccd4e51882515861d2347)
---
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
---
#### log.json 作用
* logs.json 配置生成一个界面，顶部的颜色和文字你都可以在这个 json 文件里边定义好。
* 每一个小程序页面也可以使用同名 .json 文件来对本页面的窗口表现进行配置，页面中配置项会覆盖 app.json 的 window 中相同的配置项。
---
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

---
#### 开发者工具预览时报错 iconPath="",file
> 这个 bug 的原因是 app.json 里面list的iconPath为空
---
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
---
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
---
#### 微信小程序“是否授权XXX"的询问弹窗必须绑定按钮，不能用js唤起
---
#### 获取节点高度
> 在 component 中，必须放在 pageLifetimes 的 show 方法中
```
wx.createSelectorQuery().selectAll('.page').boundingClientRect(function (rect) {
  console.log(rect[0].height)
  }).exec()  
```
---
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
---
#### 取消微信小程序自带导航栏
* 所有页面都取消
> 在app.json window 增加 navigationStyle:custom ，顶部导航栏就会消失

* 特定页面取消（比如只有 issue 页面要隐藏）
1. 保证已经在 app.json 的 pages 中注册页面
```
  "pages": [
    "pages/issue/issue"
  ],
```
2. issue.json
```
{
  "component": true,
  "usingComponents": {},
  "navigationStyle": "custom"
}
```
---
#### 在 component 中获取全局数据
```
// app.js

globalData: {
  userInfo: null,
  message:"hhh"
}
```
```
// issue.js
var appInstance = getApp()
console.log(appInstance.globalData)
```
---
#### [component 中的 slot](https://developers.weixin.qq.com/miniprogram/dev/framework/custom-component/wxml-wxss.html)
---
#### 页面栈
```
wx.navigateTo({
  url: '../issue/issue'
}) 
```
会形成页面栈，之后可用 navigateBack 返回之前的页面
```
wx.navigateBack({
  delta: 1
})
```
---
#### [showToast](https://developers.weixin.qq.com/miniprogram/dev/api/ui/interaction/wx.showToast.html)
1. icon 只接受三个值："none","success","loading"
2. duration 规定了多久后 toast 自动消失
```
wx.showToast({
  title: '发布成功',
  icon:'success',
  duration: 2000
})
```
---
#### [showModal](https://developers.weixin.qq.com/miniprogram/dev/api/ui/interaction/wx.showModal.html)
1. showCancel 为 fasle 时只显示一个"确定"按钮，且会自动居中
2. modal 不会自动消失
3. [content 内容支持换行，但是开发时编译结果无效，必须在真机上才能看到效果](https://developers.weixin.qq.com/community/develop/doc/000e4c8d13c368713f170bb8b56000)
```
 wx.showModal({
    title: '提示',
    content: '这是一个模态弹窗',
    showCancel:false,
    success (res) {
      if (res.confirm) {
        console.log('用户点击确定')
      } else if (res.cancel) {
        console.log('用户点击取消')
      }
    }
  })
```
---

#### class 绑定
```		
<view bindtap="createCard" class="editSubmit {{!actionValid?'invalid':''}}">制作电子名片</view>
```
#### style 绑定
```
<view class="nav-wrapper" style="height:{{statusHeight+40}}px">
</view>
```
---
#### 跳转到搜索页面后自动弹出键盘
* connection-search
```
 <input focus="true" />
```
---
#### bindtap 传参
> 利用 dataset 隐式传参，bindtap 的参数只能是 event,这与 vue 不同
```
<span wx:if="{{visible}}">
  <span wx:for="{{myCards}}" data-index="{{index}}" bindtap="selectCard">{{item.title}}</span>
</span>
```
```
selectCard(event){
  console.log(event.target.dataset.index)
}
```
---
#### clickOutside
> 弹窗显示后，点击除弹窗外的页面其他位置，则弹窗关闭
```
// connection.wxml

<view class="page" bindtap="clickOutside">
	<block wx:for="{{receivedCards}}">
			<view class="postback" bindtap="postback" data-class="postback">回发名片</view>
	</block>
</view>
```
```
// connection.js

  // 点击“回发名片”后，会出现名片待选项弹窗，点击除弹窗外的其他位置后，弹窗消失
  clickOutside(event){

    const targetClass=event.target.dataset.class

    if(this.data.visible){

      // 若该次 tap 的目的是 postback
      if(targetClass=='postback') return
      if(!targetClass||targetClass!="myCard"){
        this.setData({
          visible:false
        })
      }
    }

  },
```
---
#### query
```
wx.navigateTo({
   url: '../editCard/editCard?action=create'
}) 
```
* component 在 onLaod 中处理 query
```
  methods: {
    onLoad:function(query){
      const {action}=query

      this.setData({
        edit:action==='create'
      })

    },
```
---
#### component 的 attached 先于 onLoad 执行！
> 数据渲染应该放在 onLoad 中
---
#### block 不能加 class
---
#### [radio 的用法](https://developers.weixin.qq.com/miniprogram/dev/component/radio.html)
---
#### [微信小程序登录](https://developers.weixin.qq.com/miniprogram/dev/framework/open-ability/login.html#%E7%99%BB%E5%BD%95%E6%B5%81%E7%A8%8B%E6%97%B6%E5%BA%8F)
* 登录流程
1. 前端调用wx.login()，获得 code（有效期5分钟），将其返回给后端服务器。
2. 后端服务器将 code 发送给微信服务器，换取 uniunID 和 session_key。
3. 后端将 uniunID 和用户ID绑定，将用户ID返回给前端。上述1，2，3就叫做“用户登录”
4. 前端存储用户ID，之后向后端发送请求(wx.request)带上用户ID，后端根据用户ID查询对应数据库记录
5. 由于前端的用户ID不可能永久存储，所以必然有用户ID失效的时刻。此时前端需要重新调用wx.login()，待后端获取到 uniunID 后在数据库查询对应用户ID，再传给前端。这个过程就叫做“重新登录”。
----
* [wx.request](https://developers.weixin.qq.com/miniprogram/dev/api/network/request/wx.request.html)
> 用于前端发送HTTP请求，注意不是只向微信服务器发送请求。
* session_key
> 开发者服务器和微信服务器的会话密钥
---
#### wx.request
* method 为 GET 时，data 为 query 数据
* method 为 POST 时，data 为 body 数据，[且 header 值必须为 application/x-www-form-urlencoded](https://blog.csdn.net/RandomParty/article/details/79824499) 
---
#### 为什么缓存要有有效期
> 例如我们有一个新闻列表，为了更快的响应用户的请求，更快的加载页面，我们一般把列表缓存到本地，默认情况下如果本地有缓存，我们就不会重新请求 API，但是我们的新闻不可能不更新，更新后的内容就不会显示在小程序中。
---
#### [小程序如何支持 async/await](https://segmentfault.com/a/1190000021966277)
---
#### [getApp不可以在 App 调用前，以及 App 内定义的函数中使用](https://developers.weixin.qq.com/miniprogram/dev/reference/api/getApp.html)
```
// 在 App 内部，直接访问this

  onLaunch: function () {

    const userId = await this.globalData.storage.get('userId')

    }
```
---
#### 用 Promise 封装 wx.request
```
// 注意没有把 wx.request 的类型改为 Promise,只是用一个 Promise 包裹它，并根据 wx.request 的回调选择resolve ot reject 
const fetch = (api, method, data, success) => {

  return new Promise((resolve, reject) => {
    wx.request({
      url: url[api], // 就是拼接上前缀,此接口域名是开放接口，可访问
      method: method == 'POST' ? 'POST' : 'GET', // 判断请求类型，除了值等于'post'外，其余值均视作get
      data: data ? data : {},
      header: {
        'content-type': 'application/x-www-form-urlencoded'
      },
      success: (res) => {
        resolve(res)
      },
      fail: (err) => {
        reject(err)
      }
    })
  })
}
```
```
      const res = await fetch('login', 'GET', {
        code
      })
      console.log(res)
```

