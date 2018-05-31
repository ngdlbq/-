前端性能优化是一个很宽泛的概念，一般就是配合各种方式、手段、辅助系统，以提升用户体验，改善页面性能。
## 什么叫前端性能？
一个网站的性能主要关乎两项，一是加载性能、二是执行性能。前端性能可以认为是用户获取所需要页面数据或执行某个页面动作的一个实时性指标，一般以用户希望获取数据的操作到用户实际获得数据的时间间隔来衡量。
## 优化的目的是什么
1. 从用户角度而言，优化能够让页面加载得更快、对用户的操作响应得更及时，能够给用户提供更为友好的体验。
2. 从服务商角度而言，优化能够减少页面请求数、或者减小请求所占带宽，能够节省可观的资源。

综上： 提高用户体验的同时，节省相当的资源利用。
## 如何进行优化
常见手段： 减少HTTP请求数。首先，每个请求都是有成本的，既包含时间成本也包含资源成本。一个完整的请求都需要经过 DNS寻址、与服务器建立连接、发送数据、等待服务器响应、接收数据这样一个 “漫长” 而复杂的过程。
![](https://user-gold-cdn.xitu.io/2017/12/26/1609184e525a8133?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

资源合并与压缩。这里的资源一般指HTML、CSS、JS、Image，如果可以的话，尽可能的将外部的脚本、样式进行合并，多个合为一个。这些在开发过程中，可以利用 webpack 实现。

CSS雪碧图。合并 CSS图片，可以有效减少请求数。同时，对于体积较小的图片，可以转为base64格式嵌入到css或页面中，也可以有效较少请求数，但同时会增加图片体积（大约增加1/3），且不利于图片缓存。

## 开发者工具之network
浏览器的资源加载时序图可以帮助分析页面资源加载过程中的性能问题。这种方法可以粗粒度地宏观分析浏览器的所有资源文件请求耗时和文件加载顺序情况，据此有利于找出加载过程中比较耗时的文件资源，帮助我们有针对性地进行优化。
![](https://github.com/ngdlbq/font-end-performance/blob/master/src/image/%E5%9B%BE%E7%89%87%201.png?raw=true)
![](https://github.com/ngdlbq/font-end-performance/blob/master/src/image/%E5%9B%BE%E7%89%87%203.png?raw=true)
DOMContentLoaded：DOM树构建完成。即HTML页面由上向下解析HTML结构到末尾封闭标签
Load：页面加载完毕。 DOM树构建完成后，继续加载html/css 中的图片资源等外部资源，加载完成后视为页面加载完毕。

DOMContentLoaded 会比 Load 时间小，两者时间差大致等于外部资源加载的时间。

Finish： 是页面上所有 http 请求发送到响应完成的时间，HTTP1.0/1.1 协议限定，**单个域名的请求并发量是 6 个**，即 Finish 是所有请求在并发量为6的限制下完成的时间。当 Finish 的时间比 Load 大，意味着页面有相当部分的请求。据此利用浏览器并发请求的特性，优化代码，提高前端请求的并发量。

   `collect = await dataList.collectInfo(value)
    let { auditCaseNum } = await dataList.auditInfo(value)
    collect.auditCaseNum = auditCaseNum
    commit(config.commitName('collectInfo'), collect)`

    优化后：
    `Promise.all([dataList.collectInfo(value), dataList.auditInfo(value)]).then(res => {
      collect = res[0]
      collect.auditCaseNum = res[1].auditCaseNum
      commit(config.commitName('collectInfo'), collect)
    })`
![](https://github.com/ngdlbq/font-end-performance/blob/master/src/image/%E5%9B%BE%E7%89%87%205.png?raw=true)
![](https://github.com/ngdlbq/font-end-performance/blob/master/src/image/%E5%9B%BE%E7%89%87%206.png?raw=true)

## 开发者工具之performance
performance 是最好的性能优化的第一站，集中说明了你的应用程序活动,包括加载、执行脚本、渲染和重绘花费的时间，它是程序运行时的性能综述。performance只能告诉程序中哪些操作比较消耗资源，包括 js、dom 节点占用的内存情况，由 css 引起的浏览器重绘、重排，以及程序运行时消耗的时间。通过这些宏观的性能表现，可以确定程序哪部分可能存在性能问题，找出性能瓶颈。
![](https://github.com/ngdlbq/font-end-performance/blob/master/src/image/%E5%9B%BE%E7%89%877.png?raw=true)
![](https://github.com/ngdlbq/font-end-performance/blob/master/src/image/%E5%9B%BE%E7%89%878.png?raw=true)

network、performance只是宏观上对网站的网站性能进行分析，如果需要更加具体的定位到某个(些)函数引起的性能问题， 则还需结合 jsProfiler、memory 进行更深入的优化。（见下回讲解）