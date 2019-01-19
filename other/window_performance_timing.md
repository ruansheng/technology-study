## window.performance.timing
```
前端性能分析

```
```
performance = {
     // memory 是非标准属性，只在 Chrome 有
     //这个属性提供了一个可以获取到基本内存使用情况的对象
     memory: {
         usedJSHeapSize:  10000000, // JS 对象（包括V8引擎内部对象）占用的内存
         totalJSHeapSize: 10000000, // 可使用的内存
         jsHeapSizeLimit: 2190000000 // 内存大小限制
     },

     //  navigation: 提供操作（包含在 timing 里时间）的有用上下文
     //包括页面是加载还是刷新、发生了多少次重定向，等等。
     navigation: {
        redirectCount: 0, // 重定向次数
        type: 0           // 0   正常进入的页面（即非刷新、非重定向等）
                          // 1   通过 window.location.reload() (即刷新页面)
                          // 2   通过浏览器的前进后退按钮进入的页面（历史记录）
                          // 255 非以上方式进入的页面
     },

     timing: {
        // 同一个浏览器上下文的上一个文档卸载(unload)结束时的UNIX时间戳。
        //如果没有上一个文档，这个值会和fetchStart相同。
        navigationStart: 1441112691935,

        // 上一个文档unload事件抛出时的UNIX时间戳。
        //如果没有上一个文档,这个值会返回0.
        unloadEventStart: 0,

        // 和 unloadEventStart 相对应，unload事件处理完成时的UNIX时间戳。
        //如果没有上一个文档,这个值会返回0.
        unloadEventEnd: 0,

        // 第一个HTTP重定向开始时的UNIX时间戳。
        //如果没有重定向，或者重定向中的一个不同源，这个值会返回0.
        redirectStart: 0,

        // 最后一个HTTP重定向完成时（也就是说是HTTP响应的最后一个比特直接被收到的时间）的UNIX时间戳。
        //如果没有重定向，或者重定向中的一个不同源，这个值会返回0.
        redirectEnd: 0,

        // 浏览器准备好使用HTTP请求来获取(fetch)文档的UNIX时间戳。这个时间点会在检查任何应用缓存之前。
        fetchStart: 1441112692155,

        // DNS 域名查询开始的UNIX时间戳。
        //如果使用了持续连接(persistent connection)，或者这个信息存储到了缓存或者本地资源上，这个值将和fetchStart一致。
        domainLookupStart: 1441112692155,

        // DNS 域名查询完成的时间.
        //如果使用了本地缓存（即无 DNS 查询）或持久连接，则与 fetchStart 值相等
        domainLookupEnd: 1441112692155,

        // HTTP（TCP） 域名查询结束的UNIX时间戳。
        //如果使用了持续连接(persistent connection)，或者这个信息存储到了缓存或者本地资源上，这个值将和 fetchStart一致。
        connectStart: 1441112692155,

        // HTTP（TCP） 返回浏览器与服务器之间的连接建立时的Unix毫秒时间戳。
        // 如果建立的是持久连接，则返回值等同于fetchStart属性的值。
        //连接建立指的是所有握手和认证过程全部结束。
        connectEnd: 1441112692155,

        // HTTPS 返回浏览器与服务器开始安全链接的握手时的Unix毫秒时间戳。
        //如果当前网页不要求安全连接，则返回0。
        secureConnectionStart: 0,

        // 返回浏览器向服务器发出HTTP请求时（或开始读取本地缓存时）的Unix毫秒时间戳。
        requestStart: 1441112692158,

        // 返回浏览器从服务器收到（或从本地缓存读取）第一个字节时的Unix毫秒时间戳。
        //如果传输层在开始请求之后失败并且连接被重开，该属性将会被数制成新的请求的相对应的发起时间。
        responseStart: 1441112692686,

        // 返回浏览器从服务器收到（或从本地缓存读取，或从本地资源读取）最后一个字节时（如果在此之前HTTP连接已经关闭，则返回关闭时）的Unix毫秒时间戳。
        responseEnd: 1441112692687,

        // 当前网页DOM结构开始解析时（即Document.readyState属性变为“loading”、相应的 readystatechange事件触发时）的Unix毫秒时间戳。
        domLoading: 1441112692690,

        // 当前网页DOM结构结束解析、开始加载内嵌资源时（即Document.readyState属性变为“interactive”、相应的readystatechange事件触发时）的Unix毫秒时间戳。
        domInteractive: 1441112693093,

        // 当解析器发送DOMContentLoaded 事件，即所有需要被执行的脚本已经被解析时的Unix毫秒时间戳。
        domContentLoadedEventStart: 1441112693093,

        // 当所有需要立即执行的脚本已经被执行（不论执行顺序）时的Unix毫秒时间戳。
        domContentLoadedEventEnd: 1441112693101,

        // 当前文档解析完成，即Document.readyState 变为 'complete'且相对应的readystatechange 被触发时的Unix毫秒时间戳
        domComplete: 1441112693214,

        // load事件被发送时的Unix毫秒时间戳。如果这个事件还未被发送，它的值将会是0。

        loadEventStart: 1441112693214,

        // 当load事件结束，即加载事件完成时的Unix毫秒时间戳。如果这个事件还未被发送，或者尚未完成，它的值将会是0.
        loadEventEnd: 1441112693215
    }
};
```

### 网页解析时间节点
![image.png](./7e63b0174961f257d375e7f12ca790fe.png)

### 计算指标
```
页面加载完成时间
   window.performance.timing.loadEventEnd - window.performance.timing.navigationStart

解析 DOM 树结构的时间
   window.performance.timing.domComplete - window.performance.timing.responseEnd

DNS 查询时间
   window.performance.timing.domainLookupEnd - window.performance.timing.domainLookupStart

读取页面第一个字节的时间
   window.performance.timing.responseStart - window.performance.timing.navigationStart

内容加载完成的时间
   window.performance.timing.responseEnd - window.performance.timing.requestStart

TCP 建立连接完成握手的时间
   window.performance.timing.connectEnd - window.performance.timing.connectStart

执行 onload 回调函数的时间
   window.performance.timing.loadEventEnd - window.performance.timing.loadEventStart
```