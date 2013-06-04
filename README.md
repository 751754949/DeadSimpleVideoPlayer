HTML5 Video调研
=========

## 控制条与全屏幕播放

iPhone不支持控制条，且点击后直接全屏播放（不会在网页内播放）。android浏览器，大部分都支持控制条，点击控制条上的播放按钮会直接播放，点击视频部分或是全屏幕按钮会全屏幕播放（有些浏览器点击视频部分也不一定全屏幕播放）。控制条上的外观也各有不同，播放按钮、进度条、和最大化按钮是几乎都有的，播放状态指示器和音量控制则不一定有。

比较悲惨的是，android如果不默认设置显示控制条，则会永远不显示。故有些浏览器就会不能全屏播放（不包括用使用fullscreen api）。`fullscreen api`目前还在草案阶段，支持情况很差不建议使用。所以android机型目前几乎不支持通过js来激活全屏播放。

## 预览图

iPhone支持预览图，但是透明图片会被默认加上黑色背景。android有很多特殊情况，最新版UC浏览器、Android Chrome是支持预览图的（且背景色是透明的），但是低版本android的默认浏览器则不支持。

## 高宽

如果不设置默认的高宽，iPhone会自适应初始的高宽。而android浏览器则大多情况下是有个初始高宽，播放后又获取视频真实宽度然后修改自己的高宽，容易导致页面排版变乱。部分android浏览器是等待一段时间后才变成真正的高宽（不需要播放，估计是浏览器在这段时间内从服务器读取的宽高）。情况比较悲观，所以一定要设置默认高宽，避免排版悲剧。

另外有个很重要的bug：在低版本的android浏览器中，高宽如果小到一定程度（例如无法显示所有控制条和播放按钮的情况），则这两个较小的高宽会失效（或显示地很奇怪）。所以在限定一个较小高度时可以用一个外部容器设置高宽并`overflow:hidden`。

## 自动播放

iPhone不支持自动播放，android上UC浏览器与高版本的默认浏览器是支持自动播放，但是低版本的默认浏览器和非最新版的chrome则不支持。且有些支持的浏览器有很严重的bug：播放之后页面卡死，不显示视频只显示页面。只能点击“撤销”按钮才能回到原来的界面。不推荐使用！

## 编码与格式

目前测试的结果中：`type='video/mp4; codecs="avc1.42E01E, mp4a.40.2"'`在几乎所有的android（版本2.1以上）和iPhone上都可以播放的。值得注意的是：iPhone的浏览器对提供mp4的server端有一定要求，一般的静态server是满足不了。这估计和`Content-Type`[标头不正确](http://www.html5rocks.com/zh/tutorials/video/basics/#toc-markup)和Apple自己的[HTTP Live Streaming (HLS) Protocol](http://developer.apple.com/resources/http-streaming/)有些关系。

## Javascript与事件支持

总体来说iPhone与android的支持情况各有优劣。使用这些api之前一定要做好特征检测，不然很容易导致bug。例如：iPhone是不支持video.play()来激活播放的，但是它有个特点是即使被其他元素遮住，点击它所在的那个区域也是可以播放的。

## 支持情况概览

Android 2.3+的默认浏览器基本都支持mp4格式的video标签，但对全屏支持不全，且都无法通过js激活全屏。旧版本还存在一些[bug](http://www.broken-links.com/2010/07/08/making-html5-video-work-on-android-phones/)。iPhone 3.2+的默认浏览器也支持mp4格式的video标签（默认打开即调用默认播放器全屏播放，且无法通过js来播放）。

## H.264简介

H.264/MPEG-4第10部分，或称AVC（Advanced Video Coding，高级视频编码），是一种视频压缩标准，一种被广泛使用的高精度视频的录制、压缩和发布格式。第一版标准的最终草案于2003年5月完成。

H.264/MPEG-4 AVC是一种面向块的基于运动补偿的编解码器标准。由ITU-T视频编码专家组与ISO/IEC联合工作组——即动态图像专家组（MPEG）——联合组成的联合视频组（JVT，Joint Video Team）开发。因ITU-T H.264标准和 ISO/IEC MPEG-4 AVC标准（正式名称是ISO/IEC 14496-10 — MPEG-4第十部分，高级视频编码）有相同的技术内容，故被共同管理。

H.264标准可以被看作一个“标准家族”，成员有下面描述的各种配置（profile）。一个特定的解码器至少支持一种，但不必支持所有的。解码器标准描述了它可以解码哪些配置。在HTML的`source`标签的`type`属性中可以制定所使用的H.264的配置(profile)：

```html
<!-- H.264 Constrained baseline profile video (main and extended video compatible) level 3 and Low-Complexity AAC audio in MP4 container -->
<source src='video.mp4' type='video/mp4; codecs="avc1.42E01E, mp4a.40.2"'>
<!-- H.264 Extended profile video (baseline-compatible) level 3 and Low-Complexity AAC audio in MP4 container -->
<source src='video.mp4' type='video/mp4; codecs="avc1.58A01E, mp4a.40.2"'>
<!-- H.264 Main profile video level 3 and Low-Complexity AAC audio in MP4 container -->
<source src='video.mp4' type='video/mp4; codecs="avc1.4D401E, mp4a.40.2"'>
<!-- H.264 'High' profile video (incompatible with main, baseline, or extended profiles) level 3 and Low-Complexity AAC audio in MP4 container -->
<source src='video.mp4' type='video/mp4; codecs="avc1.64001E, mp4a.40.2"'>
```

codec是“[编解码器](http://zh.wikipedia.org/wiki/%E7%BC%96%E8%A7%A3%E7%A0%81%E5%99%A8)”的英文翻译。

### 视频转换

这里推荐使用[HandBrake](http://handbrake.fr/)来转换视频，因为它可以选择使用那种profile。步骤如下：

1. 菜单 File > Source 选择你要转换的视频
2. 选择 Format 为MP4，选中“Web Optimized”勾选框
3. 进入video标签，选择H.264 Profile为“Baseline”，level为“3.0”
4. 进入audio标签，选择Encoder为AAC(faac)

这样得到的转换后视频符合`video/mp4; codecs="avc1.42E01E, mp4a.40.2"`格式。

## 具体方案

按照目前的支持情况，需要针对iPhone和android做不同的处理。大概流程为：

1. 首先用javascript判断浏览器能否播放`video/mp4; codecs="avc1.42E01E, mp4a.40.2"`格式
2. 如果能播放，则显示封面预览图和播放按钮
    1. 在iPhone 3.2+下点击直接全屏幕播放
    2. 在android 2.3+下点击进入一个新页面播放。新页面中的播放器框会更大些，且显示播放器默认控制条，方便用户手动最大化
3. 如果浏览器不能播放，则仅仅显示封面图片

这个方案需要浏览器支持javascript，预估能cover到大多数“高端浏览器（iphone & android）”。这里有一个简单的[demo](http://nodejs.in/DeadSimpleVideoPlayer/)。

## 工具与参考链接

* [The State Of HTML5 Video](http://www.longtailvideo.com/html5/) 目前最全的video支持状况表
* [can I use video](http://caniuse.com/video)
* [video for every body](http://camendesign.com/code/video_for_everybody) 用flash实现的video兼容方案，不过我们不适用
* [HTML5视频教程](http://www.html5rocks.com/zh/tutorials/video/basics/#toc-markup)
* [Web specifications support in Opera products](http://www.opera.com/docs/specs/productspecs/)
* [easyhtml5video](http://easyhtml5video.com/) 转换视频格式的工具
* [很多有用的转码工具](http://www.broken-links.com/2010/07/30/encoding-video-for-android/)
* [android版本占比](http://developer.android.com/about/dashboards/index.html)
* [Unsolved HTML5 video issues on iOS](http://blog.millermedeiros.com/unsolved-html5-video-issues-on-ios/)
* [Dive into HTML5](http://diveintohtml5.info/video.html)
* 几个简单的播放器：[MediaElement.js](http://mediaelementjs.com/)、[videojs](http://www.videojs.com/)
* [视频编解码 wiki](http://zh.wikipedia.org/wiki/%E8%A7%86%E9%A2%91%E7%BC%96%E8%A7%A3%E7%A0%81%E5%99%A8)
* [H.264 wiki](https://zh.wikipedia.org/wiki/H.264/MPEG-4_AVC)
* [HTML5 source标签标准](http://dev.w3.org/html5/spec-preview/the-source-element.html)

