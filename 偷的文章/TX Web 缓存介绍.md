### **TX Web 缓存介绍** 

- Web 缓存是指一个 Web 资源（如 html 页面，图片，js，数据等）存在于 Web 服务器和客户端（浏览器）之间的副本。
- 缓存会根据进来的请求保存输出内容的副本；当下一个请求来到的时候，如果是相同的 URL，缓存会根据缓存机制决定是直接使用副本响应访问请求，还是向源服务器再次发送请求。

### **Web 缓存的好处**

- 减少网络延迟，加快页面打开速度
- 减少网络带宽消耗
- 降低服务器压力
- ...

### **HTTP 的缓存机制**

#### 简化的流程如下

![图片](https://mmbiz.qpic.cn/mmbiz_png/j3gficicyOvauoO9M7yHKI0dOlVAq0Xpicl2dTzIib7UTypX0ad5zibFPqWpice4ghG0qoTRjMDOLOIfImI38yGsVe7A/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

#### 根据什么规则缓存

1. 新鲜度（过期机制）：也就是缓存副本有效期。一个缓存副本必须满足以下条件，浏览器会认为它是有效的，足够新的：

- 含有完整的过期时间控制头信息（HTTP 协议报头），并且仍在有效期内；
- 浏览器已经使用过这个缓存副本，并且在一个会话中已经检查过新鲜度；

1. 校验值（验证机制）：服务器返回资源的时候有时在控制头信息带上这个资源的实体标签 Etag（Entity Tag），它可以用来作为浏览器再次请求过程的校验标识。如果发现校验标识不匹配，说明资源已经被修改或过期，浏览器需求重新获取资源内容。

#### HTTP 缓存的两个阶段

浏览器缓存一般分为两类：强缓存（也称本地缓存）和协商缓存（也称弱缓存）。

##### **本地缓存阶段**

浏览器发送请求前，会先去缓存里查看是否命中强缓存，如果命中，则直接从缓存中读取资源，不会发送请求到服务器。否则，进入下一步。

##### **协商缓存阶段**

当强缓存没有命中时，浏览器一定会向服务器发起请求。服务器会根据 Request Header 中的一些字段来判断是否命中协商缓存。如果命中，服务器会返回 304 响应，但是不会携带任何响应实体，只是告诉浏览器可以直接从浏览器缓存中获取这个资源。如果本地缓存和协商缓存都没有命中，则从直接从服务器加载资源。

##### **启用&关闭缓存**

按照本地缓存阶段和协商缓存阶段分类：

![图片](https://mmbiz.qpic.cn/mmbiz_png/j3gficicyOvauoO9M7yHKI0dOlVAq0XpiclL9dziaLLFq8BaRDjPBPdS7ztAZgy6UTZl79lI014DIAF9q9xtRibCriag/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

1. 使用 HTML Meta 标签 　　 Web 开发者可以在 HTML 页面的节点中加入标签，如下：![图片](https://mmbiz.qpic.cn/mmbiz_png/j3gficicyOvauoO9M7yHKI0dOlVAq0XpiclkWb5hia8KG2BibXIj0W6xR6icO4rqWia0VwkFV5b44mBYk3u6cw3xnJQIw/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

上述代码的作用是告诉浏览器当前页面不被缓存，事实上这种禁用缓存的形式用处很有限：

a. 仅有 IE 才能识别这段 meta 标签含义，其它主流浏览器仅识别“Cache-Control: no-store”的 meta 标签。

b. 在 IE 中识别到该 meta 标签含义，并不一定会在请求字段加上 Pragma，但的确会让当前页面每次都发新请求（仅限页面，页面上的资源则不受影响）。

1. 使用缓存有关的 HTTP 消息报头 这里需要了解 HTTP 的基础知识。一个 URI 的完整 HTTP 协议交互过程是由 HTTP 请求和 HTTP 响应组成的。有关 HTTP 详细内容可参考《Hypertext Transfer Protocol — HTTP/1.1》、《HTTP 权威指南》等。

在 HTTP 请求和响应的消息报头中，常见的与缓存有关的消息报头有：

![图片](https://mmbiz.qpic.cn/mmbiz_png/j3gficicyOvauoO9M7yHKI0dOlVAq0Xpiclt2yHdYg7kCONXI2PFh1bpy9anJfkIibBsVO7rWSac2WrM2D7Ko0flvg/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

上图中只是常用的消息报头，下面来看下不同字段之间的关系和区别：

1. Cache-Control 与 Expires

2. - Cache-Control：HTTP1.1 提出的特性，为了弥补 Expires 缺陷加入的，提供了更精确细致的缓存功能。[详细了解](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers/Cache-Control)详细看几个常见的指令：_ max-age：功能和 Expires 类似，但是后面跟一个以“秒”为单位的相对时间，来供浏览器计算过期时间。_ no-cache：提供了过期验证机制。![图片](https://mmbiz.qpic.cn/mmbiz_png/j3gficicyOvauoO9M7yHKI0dOlVAq0XpiclJiawHa5jHI2oicyRcGXnfiamEt3UoYk0O7K7BM1RU0zERu61PfXKNjDzw/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)(在 Chrome 的 devtools 中勾选 Disable cache 选项，发送的请求会去掉 If-Modified-Since 这个 Header。同时设置 Cache-Control:no-cache Pragma:no-cache，每次请求均为 200) 

- - no-store：表示当前请求资源禁用缓存；
  - public：表示缓存的版本可以被代理服务器或者其他中间服务器识别；
  - private：表示只有用户自己的浏览器能够进行缓存，公共的代理服务器不允许缓存。

- Expires：HTTP1.0 的特性，标识该资源过期的时间点，它是一个绝对值，格林威治时间（Greenwich Mean Time, GMT），即在这个时间点之后，缓存的资源过期；优先级：Cache-Control 优先级高于 Expires，为了兼容，通常两个头部同时设置；浏览器默认行为：其实就算 Response Header 中沒有设置 Cache-Control 和 Expires，浏览器仍然会缓存某些资源，这是浏览器的默认行为，是为了提升性能进行的优化，每个浏览器的行为可能不一致，有些浏览器甚至没有这样的优化。

1. Last-Modified 与 ETag

2. - Last-Modified(Response Header)与 If-Modified-Since(Request Header)是一对报文头，属于 http 1.0。

     If-Modified-Since 是一个请求首部字段，并且只能用在 GET 或者 HEAD 请求中。Last-Modified 是一个响应首部字段，包含服务器认定的资源作出修改的日期及时间。当带着 If-Modified-Since 头访问服务器请求资源时，服务器会检查 Last-Modified，如果 Last-Modified 的时间早于或等于 If-Modified-Since 则会返回一个不带主体的 304 响应，否则将重新返回资源。![图片](https://mmbiz.qpic.cn/mmbiz_png/j3gficicyOvauoO9M7yHKI0dOlVAq0XpicllTe0GEszv8UH4yaB8ibMrXubpIuPyhhlt3r8Hm0PkRxaeOtBdm0MY3Q/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

     (注意：在 Chrome 的 devtools 中勾选 Disable cache 选项后，发送的请求会去掉 If-Modified-Since 这个 Header。)

- ETag 与 If-None-Match 是一对报文头，属于 http 1.1。

  ETag 是一个响应首部字段，它是根据实体内容生成的一段 hash 字符串，标识资源的状态，由服务端产生。If-None-Match 是一个条件式的请求首部。如果请求资源时在请求首部加上这个字段，值为之前服务器端返回的资源上的 ETag，则当且仅当服务器上没有任何资源的 ETag 属性值与这个首部中列出的时候，服务器才会返回带有所请求资源实体的 200 响应，否则服务器会返回不带实体的 304 响应。![图片](https://mmbiz.qpic.cn/mmbiz_png/j3gficicyOvauoO9M7yHKI0dOlVAq0XpiclZBMrz3Np0cRll1XZXUr6N4C3tT6ibgticG3HQlBUZWnhR7K2HInbia61g/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

- ETag 能解决什么问题？

a. Last-Modified 标注的最后修改只能精确到秒级，如果某些文件在 1 秒钟以内，被修改多次的话，它将不能准确标注文件的新鲜度；

b. 某些文件也许会周期性的更改，但是他的内容并不改变(仅仅改变的修改时间)，但 Last-Modified 却改变了，导致文件没法使用缓存；

c. 有可能存在服务器没有准确获取文件修改时间，或者与代理服务器时间不一致等情形。

- 优先级：ETag 优先级比 Last-Modified 高，同时存在时会以 ETag 为准。

  ![图片](https://mmbiz.qpic.cn/mmbiz_png/j3gficicyOvauoO9M7yHKI0dOlVAq0XpiclIzwQaibszEfdBPr4JB2xk5pFGABKlic4uD9VHYv8vpr2YmiaYib8FyAHzg/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

##### **缓存位置**

浏览器可以在内存、硬盘中开辟一个空间用于保存请求资源副本。我们经常调试时在 DevTools Network 里看到 Memory Cache（內存缓存）和 Disk Cache（硬盘缓存），指的就是缓存所在的位置。请求一个资源时，会按照优先级（Service Worker -> Memory Cache -> Disk Cache -> Push Cache）依次查找缓存，如果命中则使用缓存，否则发起请求。这里先介绍 Memory Cache 和 Disk Cache。

**200 from memory cache**

表示不访问服务器，直接从内存中读取缓存。因为缓存的资源保存在内存中，所以读取速度较快，但是关闭进程后，缓存资源也会随之销毁，一般来说，系统不会给内存分配较大的容量，因此内存缓存一般用于存储较小文件。同时内存缓存在有时效性要求的场景下也很有用（比如浏览器的隐私模式）。

**200 from disk cache**

表示不访问服务器，直接从硬盘中读取缓存。与内存相比，硬盘的读取速度相对较慢，但硬盘缓存持续的时间更长，关闭进程之后，缓存的资源仍然存在。由于硬盘的容量较大，因此一般用于存储大文件。

下图可清晰看出差别：

![图片](https://mmbiz.qpic.cn/mmbiz_png/j3gficicyOvauoO9M7yHKI0dOlVAq0XpicldKoC75YQVP3BPsO2EYTrmNAUqJPlQMW6MjKpyKwooicTvcd8QNiaJWnw/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

**200 from prefetch cache**

在 preload 或 prefetch 的资源加载时，两者也是均存储在 http cache，当资源加载完成后，如果资源是可以被缓存的，那么其被存储在 http cache 中等待后续使用；如果资源不可被缓存，那么其在被使用前均存储在 memory cache。

![图片](https://mmbiz.qpic.cn/mmbiz_png/j3gficicyOvauoO9M7yHKI0dOlVAq0XpiclZrOoWUPfCqhJVm58Q60nDGrTuEgxCxS8iaRLLtjvgajB68xHvxtyesA/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

**CDN Cache**

以腾讯 CDN 为例：X-Cache-Lookup:Hit From MemCache 表示命中 CDN 节点的内存；X-Cache-Lookup:Hit From Disktank 表示命中 CDN 节点的磁盘；X-Cache-Lookup:Hit From Upstream 表示没有命中 CDN。

![图片](https://mmbiz.qpic.cn/mmbiz_png/j3gficicyOvauoO9M7yHKI0dOlVAq0XpiclVtfrVGwtf6ByL5WzbO72SqtulM3UAtyicd4Lf6uQJ1k7iaL6tkPWVvxQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

##### **整体流程**

![图片](https://mmbiz.qpic.cn/mmbiz_png/j3gficicyOvauoO9M7yHKI0dOlVAq0XpiclcNzzT2CGriaYRRGZgpicGnZcGmrUvUuKmw82NcbibFhj9u5QTzmV3tTuA/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

从上图能感受到整个流程，比如常见两种刷新场景：

- 当 F5 刷新网页时，跳过强缓存，但是会检查协商缓存；
- 当 Ctrl + F5 强制刷新页面时，直接从服务器加载，跳过强缓存和协商缓存

### **其他 Web 缓存策略**

#### IndexDB

IndexedDB 就是浏览器提供的本地数据库，能够在客户端存储可观数量的结构化数据，并且在这些数据上使用索引进行高性能检索的 API。

异步 API 方法调用完后会立即返回，而不会阻塞调用线程。要异步访问数据库，要调用 window 对象 indexedDB 属性的 open() 方法。该方法返回一个 IDBRequest 对象 (IDBOpenDBRequest)；异步操作通过在 IDBRequest 对象上触发事件来和调用程序进行通信。

常用异步 API 如下：

![图片](https://mmbiz.qpic.cn/mmbiz_png/j3gficicyOvauoO9M7yHKI0dOlVAq0XpicluibibPoSrKtH8eiagOwZWGIk5BH8xNLKTGGMTRVl5wG0tSLbnnLEpCWwQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

在 16 年曾基于 IndexDB 做过一整套缓存策略，有不错的优化效果：

![图片](https://mmbiz.qpic.cn/mmbiz_png/j3gficicyOvauoO9M7yHKI0dOlVAq0Xpicl228s32dcxNxol64cxugwOp1SYelEnPKxelBb15lLiburLZllD1icq7nA/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

#### Service Worker

SW 从 2014 年提出的草案到现在已经发展很成熟了，基于 SW 做离线缓存，让用户能够进行离线体验，消息推送体验，离线缓存能力涉及到 Cache 和 CacheStorage 的概念，篇幅有限，不展开了。

#### LocalStorage

localStorage 属性允许你访问一个 Document 源(origin)的对象 Storage 用于存储当前源的数据，除非用户人为清除(调用 localStorage api 或则清除浏览器数据)， 否则存储在 localStorage 的数据将被长期保留。

#### SessionStorage

sessionStorage 属性允许你访问一个 session Storage 对象，用于存储当前会话的数据，存储在 sessionStorage 里面的数据在页面会话结束时会被清除。页面会话在浏览器打开期间一直保持，并且重新加载或恢复页面仍会保持原来的页面会话。

### **定义最优缓存策略**

- 使用一致的网址：如果您在不同的网址上提供相同的内容，将会多次获取和存储该内容。注意：URL 区分大小写！
- 确定中继缓存可以缓存哪些资源：对所有用户的响应完全相同的资源很适合由 CDN 或其他中继缓存进行缓存；
- 确定每个资源的最优缓存周期：不同的资源可能有不同的更新要求。审查并确定每个资源适合的 max-age；
- 确定网站的最佳缓存层级：对 HTML 文档组合使用包含内容特征码的资源网址以及短时间或 no-cache 的生命周期，可以控制客户端获取更新的速度；
- 更新最小化：有些资源的更新比其他资源频繁。如果资源的特定部分（例如 JS 函数或一组 CSS 样式）会经常更新，应考虑将其代码作为单独的文件提供。这样，每次获取更新时，剩余内容（例如不会频繁更新的库代码）可以从缓存中获取，确保下载的内容量最少；
- 确保服务器配置或移除 ETag：因为 Etag 跟服务器配置有关，每台服务器的 Etag 都是不同的；
- 善用 HTML5 的缓存机制：合理设计启用 LocalStorage、SessionStorage、IndexDB、SW 等存储，会给页面性能带来明显提升；
- 结合 Native 的强大存储能力：善于利用客户端能力，定制合适的缓存机制，打造极致体验。

### **结语**

通过了解浏览器各种缓存机制和存储能力特点，结合业务制定合适的缓存策略，善用缓存是基本功，可以用于时常审查负责的业务，可能就会发现个别业务并没有运用到位，共勉。