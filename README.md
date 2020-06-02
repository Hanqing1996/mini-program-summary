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

