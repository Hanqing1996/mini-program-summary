[参考](https://juejin.im/post/5b0ccd4e51882515861d2347)

---
#### 使用 we-ui 组件
```
// app.json

"useExtendedLib": {
"weui": true
}
```
```
// professional-identity.json

  "usingComponents": {
    "mp-actionSheet": "/miniprogram_npm/weui-miniprogram/actionsheet/actionsheet"
  }
```
```
// professional-identity.WXML

    <mp-actionSheet bindactiontap="btnClick" show="{{showActionsheet}}" actions="{{groups}}" title="这是一个标题，可以为一行或者两行。">
    </mp-actionSheet>
```



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
---
#### 定义全局方法
```
// app.js
App({
    login:function(){
    }
})
```
```
// home.js
const app = getApp()
app.login()
```
---
#### [wx.navigateTo从子页面跳回父页面，页面不更新的问题](https://blog.csdn.net/qq_35585701/article/details/81162574)
在 zhq-mail  中，我们是使用 vue-router 的路由守卫来进行更新的
---
#### onLoad 和 onLaunch 的执行先后是不一定的（都是异步任务）
> 所以我将 login,auth 等启动时任务放在了 home.js 的 onload 中
---
#### switchTab 的 url 要与 app.js 中一致（但要加前缀/）
```
// app.json
      {
        "text": "首页",
        "pagePath": "pages/home/home",
        "selectedIconPath": "/image/trend.png",
        "iconPath": "/image/trend.png"
      },
````
```
wx.switchTab({
    url: '/pages/home/home'
})
```
---
#### [阻止事件冒泡](https://developers.weixin.qq.com/miniprogram/dev/framework/view/wxml/event.html)
在处理 clickOutside 时有用到
---
#### [局域网通信](https://developers.weixin.qq.com/miniprogram/dev/framework/ability/mDNS.html)
* 当且仅当 IP 与手机 IP 处在同一网段且不与本机 IP 相同（一般来说，就是同一局域网，如连接在同一个 wifi 下）时，请求/连接才会成功。
* 在这种情况下，不会进行安全域的校验，不要求必须使用 https/wss，也可以使用 http/ws。
---
#### [小程序app.wxss中的样式在自定义组件不生效](https://developers.weixin.qq.com/community/develop/doc/0008c20470c01023c246e588c50800)
---
#### [组件间通信](https://developers.weixin.qq.com/miniprogram/dev/framework/custom-component/events.html)
---
#### 分享小程序给微信好友
> 点击带有  open-type="share" 的 button,就会触发 onShareAppMessage
```
<button open-type="share">发名片</button>
```
```
onShareAppMessage: function (res) {
    const {currentCard}= this.data
    const {cardIndex,userId,username}=currentCard

    return {
	title: `我是${username},邀请您与我交换名片`,
	path: `/pages/cardDetail/cardDetail?cardIndex=${cardIndex}&&userId=${userId}`
    }
}
```
---
#### observer 的用法
* 正确用法
> currentCard 始终为 this.data.cards[selected]，所以可以视 currentCard 为一个计算属性
```
'selected': function (selected) {
    this.setData({
	currentCard: this.data.cards[selected]
    })
}
```
如果是某个变量变化后，我们想采取进一步操作。但是该某个变量发生变化的时机很重要（比如是不是onLoad时变化）,那么不要放入 observer 中。
---
#### [点击发布以后用wx.showToast显示发布成功后跳转](https://blog.csdn.net/LimonSea/article/details/84990051)
---
#### [数据预拉取，可能有助于解决首页状态有切换的问题](https://developers.weixin.qq.com/miniprogram/dev/framework/ability/pre-fetch.html)
---
#### 到底拉取数据
```
// share.js
onReachBottom(){
}
```
```
// share.json
{
	"onReachBottomDistance":10
}
```
---
#### [无法使用useExtendedLib 扩展库引入的weui](https://developers.weixin.qq.com/community/develop/doc/0000ea7ef7cf28e2044ab4ebb54400)
---
#### input 
* 双向绑定
```
<input type="text" value="{{newCommentValue}}" bindinput="getInputValue"/>
```
```
data: {
  newCommentValue: ''
},
getInputValue(event) {
    const {value: newCommentValue} = event.detail
    this.setData({newCommentValue})
}
```
* focus:自动聚焦
```
<input type="text" focus="{{focus}}"/>
```
```
data:{
 focus: false
}
```
#### [open-data的userAvatarUrl怎么做成圆角](https://developers.weixin.qq.com/community/develop/doc/0002ead6ec8e50353fa7ccf6556800)
```
<view class='user-img'>

<open-data   type="userAvatarUrl"></open-data>

</view>


.user-img{

width: 250rpx;

height: 250rpx;

border-radius: 50%;

overflow: hidden;

}
```
---
#### 列表渲染
* wx:for="{{positionsInCompanies}}"
指定数据源
* wx:for-item="company"
指定数组元素名为 company
* wx:for-index="companyIndex"
指定数组下标名为 companyIndex
> 注意不可写成 wx:for-index="company-index"

```
<block wx:for="{{positionsInCompanies}}" wx:for-item="company"  wx:for-index="companyIndex">
{{companyIndex}}
<block wx:for="{{company.positions}}" wx:for-item="positionName"  wx:for-index="positionIndex">
    <view class="edidcard_row">
	{{positionIndex}}
	<label>职位：</label>
	<input type="text" placeholder-class="place-holder" placeholder="请填写职位" value="{{positionName}}" bind:input="updatePosition" data-positionIndex="{{positionIndex}}"/>
	<image class="add" src="{{imageUrlPrefix}}/add-address.svg" bindtap="addPosition"></image>
    </view>
</block>
<view class="edidcard_row">
    <label>单位：</label>
    <input type="text" placeholder-class="place-holder" placeholder="请填写单位" value="{{company.companyName}}"/>
    <image class="add" src="{{imageUrlPrefix}}/add-address.svg"></image>
</view>
</block>
```
---
#### dataset 的坑
> 传到 js 里的字段没有大写
```
<image data-companyIndex="{{companyIndex}}" bindtap="addCompany"></image>
```
```
addCompany(event){
    const {companyindex}=event.target.dataset
}
```
---
```
<block wx:for="{{myCompany.positions}}" wx:for-item="positionName">
	<span class="row-container" style="justify-content: center">
	    <span class="home-item">{{positionName}}</span>
	    <image class="uncheck" src="{{imageUrlPrefix}}/uncheck.svg"></image>
	</span>
</block>
```
等价于
```
<span  wx:for="{{myCompany.positions}}" wx:for-item="positionName" class="row-container" style="justify-content: center">
    <span class="home-item">{{positionName}}</span>
    <image class="uncheck" src="{{imageUrlPrefix}}/uncheck.svg"></image>
</span>
```
---
#### onLoad 和 onShow
* 第一次进入某个页面，onLoad 和 onShow 都会被触发
* 由A页面通过navigateTo进入B页面后，通过B页面左上角返回键可返回A页面，且只触发A页面的 onShow。此种情况下，若B页面的相关操作导致A页面的对应数据更新（比如B为提交职业认证页面，A为已提交职业列表），则需要在A页面的onShow函数中拉取最新数据。
```
data: {
    shares: [],
    loading: false,
    segment:0,
    imageUrlPrefix: ''
},
methods: {
    async onLoad() {
        setImageUrlPrefix.call(this)
        
        this.setData({loading: true})
        await this.fetchData()
        this.alwaysUpdateRelativeData()
        this.setData({loading: false})
    },
    async onShow() {
        
        // onload 正在执行，不必更新
        if (this.data.loading) return
        // 更新UI数据
        await this.fetchData()
        this.alwaysUpdateRelativeData()
        
    }
}
```
* tab 页面间的切换，不会触发 onLoad
---
#### wx.redirectTo 和 wx.navigateTo
* 点击 D 页面的返回键，返回 B 页面
```
A navigateTo B redirectTo C redirectTo D
```
* 点击 C 页面的返回键，返回 B 页面
```
A navigateTo B navigateTo C
```
---
#### input 禁止弹出键盘（也就是说 input 不用于输入）
```
<view class="row-container option" bindtap="showActionSheet">
    <label style="margin-right: 20rpx">公司名称</label>
    <input placeholder="请选择您所在的公司名称" model:value="{{companyName}}" disabled='disabled'/>
</view>
```
---
#### isLoading,isEmpty 状态
```
<block wx:if="{{isLoading}}">
    加载中
</block>
<block wx:elif="{{isEmpty}}">
数据为空
</block>
<block wx:else>
</block>
```
---
#### 解决 wx.switchTab 不可以带 query 参数的问题
其实算不上解决办法，只是把 query 参数放到全局的 app.globalData 里面。wx.switchTab 处写，tab 页面读
```javascript
// app.js

App({
    globalData: {
    	...
        tempQuery:{},
    },
...    
```
```javascript
// editCard.js

app.globalData.tempQuery={
    IndexFromEditCard:cardIndex
}

wx.switchTab({
    url: `/pages/home/home`
})
```
```javascript
// home.js

async onShow() {
    const {IndexFromEditCard}=app.globalData.tempQuery
    app.globalData.tempQuery={} // 记得重置
}
```
---
#### canvas 踩坑
> 业务需要个性化分享名片时的小程序封面。想到的办法是每次绘制包含名片数据的 canvas，然后转成图片。实践中采用了[wxml-to-canvas](https://developers.weixin.qq.com/miniprogram/dev/extended/component-plus/wxml-to-canvas.html) 这个包

这里有几点要注意：
1. wxml-to-canvas 是通过 npm 安装的。而想[让小程序支持 npm](https://developers.weixin.qq.com/miniprogram/dev/devtools/npm.html)，除了**详情->勾选“使用npm模块”**外，还需要“工具->构建npm”（坑爹文档没说）
2. wxml 模板只支持 view、text、image 三种标签
3. 由 canvas 得到图片这一步要有延时，因为 canvas 的绘制本身是比较耗时的。如果 canvas 没有生成完毕，就生成图片，得到的图片可能是空白或者只有部分内容的。
```javascript
updateCover:async function(){
    // 生成 canvas
    this.renderToCanvas()


    setTimeout(async ()=>{
	const src=await this.extraImage()

	this.setData({src})

	const pictureURL=await uploadImage(this.data.src)


	console.log('图片路径');
	console.log(pictureURL);
    },3000)
```





