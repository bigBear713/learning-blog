## 缓存
- 对一个网站来说,缓存是十分重要的,影响着响应速度,影响用户体验.
- 缓存分为强缓存和协商缓存.

---

## 强缓存
- 强缓存是直接使用浏览器的缓存数据,而不进行http请求.
- 强缓存主要有两种:`expired`和`cache-control`.
- 当两种同时存在时,`cache-control`的优先级更高,因为它更稳定,准确.

---

### expired
- `expired`设置的是数据的过期时间.当需要数据时,浏览器会根据`当前时间`和数据设置的`expired`进行判断.如果当前时间在过期时间之前,则直接使用缓存数据;如果当前时间在过期时间之后,则发起http请求获取最新的数据,同时更新`expired`的值,等待下次比较.
- 因为当前时间是直接取,所以存在当前时间不准确的问题(如改电脑上的时间),因此这种方式不太严谨.
- 这是http1.0的产物

### cache-control
- `cache-control`是一个头部属性,能设置数据的有效时间(max-age),限制缓存位置(private/public),是否强缓存(no-cache),是否完全不缓存(no-store).
- max-age: 设置缓存数据的有效时间,单位是`s`.浏览器根据响应时的时间点和该参数,计算出过期时间,从而决定是使用缓存还是发起http请求获取最新数据.因为是动态计算,而不是直接取本地时间,所以更严谨.
- private/public:限制缓存位置.因为浏览器和服务器之间可能存在代理服务器,所以代理服务器也可能对数据进行缓存.当设置为`private`时,代理服务器不允许缓存数据,设置为`public`时,代理服务器可以缓存数据.
- no-chche:设置是否使用强缓存.如果不允许使用强缓存,则会使用协商缓存.
- no-store:设置是否使用缓存.如果不允许使用缓存,包括强缓存和协商缓存,都不能使用.
- 请求更新数据后,max-age等属性会被设置为新的值,以待下次使用.

---

## 协商缓存
- 协商缓存是仍会向服务器发起请求.但是服务器会进行验证,如果结果是数据没有改变,只返回304状态码和空响应体,让浏览器使用本地的缓存数据.
- 协商缓存主要分为两种:`last-modified`和`etag`, 主要是修改时间和数据唯一id的区别.服务器通过这两个来判断数据是否发生了变化。
- 如果两者同时存在, `etag`的优先级更高

### last-modified
- `last-modified`记录的是数据最近一次的修改时间.如果请求时,数据从上次修改后没有再被修改,则不返回最新数据,而是返回304,让浏览器使用缓存数据.
- 所以发起http请求时会带上这个时间,放在请求头中的`last-modified-since`属性中,以便服务器进行比较.
- 如果发生过改变,则返回最新的数据和last-modified,以便下次比较.

### etag
- `etag`是服务器为数据生成的唯一id,一旦数据发生改变,则会自动生成一个etag.如果请求时,服务器发现etag不一致,说明数据发生改变,就会返回最新的数据和相应的etag.如果一致则返回304,让浏览器使用哦缓存数据.

### last-modified和etag的优缺点
- `last-modified`对服务器压力会小一点,只需要记录修改时间.但是准确性会低一点.
- 如果打开缓存文件,就算什么也没改,`last-modified`也会改变,导致缓存失效,影响判断.
- `last-modified`的单位是`s`,所以如果这段时间内发生多次修改,无法体现出来.
- `etag`对服务器压力会大一点,没修改一次服务器都会生成一次id,但是准确性高一点.

---

## 缓存位置
- 缓存数据的存放位置主要有4种: memory cache内存缓存, disk cache硬盘缓存,service worker和push cache推送缓存.
  
### memory cache
- 将缓存数据存放在内存中.
- 优点: 读取快
- 缺点: 保存时间不长, 页面关闭时自动清除释放资源. 当内存占用率高时,缓存的数据也容易被清除以释放资源.
- 主要用户缓存已经被下载的图片,js,css等数据
- 缓存时主要分为两种方式:preloader, preloade.
  1. preloader:一边解析js和css,一边下载其它内容,是前端页面优化的主要方式之一.
  2. preloade: 显示指定预加载哪些内容

### disk cache
- 将缓存数据存放在硬盘上
- 优点: 保存时间长,页面关闭后数据依然存在,不用担心被清除以释放资源;能缓存的数据也比较多
- 缺点: 读取速度相对慢一点

### service worker
- 一种运行在浏览器背后的独立线程,脱离了浏览器窗体,因此也不能操作dom. 
- 主要实现离线缓存,消息推送,网络代理等功能.
- 因为能实现网络代理拦截,因此对安全性要求比较高,因此只能使用https.
- 它还能提供灵活的操作缓存的功能,比如缓存哪些文件,如果匹配缓存,因此是pwa实现的重要运行机制

### push cache
- 这是最后一道防线,只有在前面3种方式都失效的情况下才起作用
- 只会在session中存在,一旦会话结束,就会被释放掉.
- 是http2.0的产物