## Http

Http即HyperText Transfer Protocal，超文本传输协议，相比于传统文本，可以传输图片及多媒体等信息内容。

Http是七层（应用层）协议，[基础网络协议分层](http://example.com)

Http是一个基于请求/响应（Request/Response）模式的、无状态的协议。客户端Client发起Request请求，服务端Server处理请求后返回Response响应。

### URL

URL是请求的地址，即客户端Client请求发送的地址，我们打开一个网页简单来说就是发送了一个Http请求，网页的地址一般如下格式：

> scheme://host:port/path/…/?query-string

名称 | 功能
------------- | -------------
scheme | 资源协议，如：http，https，FTP等
host | Http 服务器的 IP 地址或域名
port | Http 服务器的默认端口是 80，这种情况下端口号可以省略，如果使用了别的端口，必须指明，例如www.sohu.com:8080
path | 访问资源的路径
query-string | 传递的参数

如下示例：

![Http URL示例](http://example.com/logo.png)

### 请求报文&响应报文示例

Http报文结构为header + body，具体格式:
> 起始行 + 头部 + 空行 + 实体

##### 起始行

> GET / Http/1.1
> 方法 + 路径 + Http版本

#### 头部
> Host: www.baidu.com
> User-Agent: curl/7.51.0
> Accept: */*

格式：
* 字段名不区分大小写
* 字段名不允许出现空格，不可以出现下划线_
* 字段名后面必须紧接着:

#### 空行

用以区分开**头部**和**实体**

#### 实体
数据部分，即body，请求报文对应**请求体**, 响应报文对应**响应体**。


![请求报文示例](http://example.com/logo.png)


![响应报文示例](http://example.com/logo.png)

### 请求方法

http/1.1规定了以下请求方法(注意，都是大写):

* GET: 通常用来获取资源
* POST: 提交数据，即上传数据
* PUT: 修改数据
* DELETE: 删除资源(几乎用不到)
* HEAD: 获取资源的元信息
* CONNECT: 建立连接隧道，用于代理服务器
* OPTIONS: 列出可对资源实行的请求方法，用来跨域请求
* TRACE: 追踪请求-响应的传输路径

GET/POST/PUT/DELETE是最常用的内容增删改查方法。一般情况下我们编写的服务端用的最多的是GET和POST，GET负责查找信息并返回，POST服务创建、修改和删除等非幂等操作。

对比两者的区别：

* Get请求可以缓存，Post不能
* Post相对Get更安全一些，因为Get请求的参数都写在URL里，并会被浏览器保存历史纪录，Post的请求参数会放在body里（当然被抓包后一样可以获取，想保证更高安全性要升级到Https）
* Post可以通过 request body来传输比 Get 更多的数据，Get受限于URL长度限制 
* Post 支持更多的编码类型且不对数据类型限制

### 常见状态码

响应结果的状态码反映了服务端对请求的处理结果，常见的状态码及说明如下：

* 2XX 成功

> 200 OK，表示从客户端发来的请求在服务器端被正确处理
> 204 No content，表示请求成功，但响应报文不含实体的主体部分
> 206 Partial Content，进行范围请求

* 3XX 重定向

> 301 moved permanently，永久性重定向，表示资源已被分配了新的 URL
> 302 found，临时性重定向，表示资源临时被分配了新的 URL
> 303 see other，表示资源存在着另一个 URL，应使用 GET 方法丁香获取资源
> 304 not modified，表示服务器允许访问资源，但因发生请求未满足条件的情况
> 307 temporary redirect，临时重定向，和302含义相同

* 4XX 客户端错误

> 400 bad request，请求报文存在语法错误
> 401 unauthorized，表示发送的请求需要有通过 Http 认证的认证信息
> 403 forbidden，表示对请求资源的访问被服务器拒绝
> 404 not found，表示在服务器上没有找到请求的资源

* 5XX 服务器错误

> 500 internal sever error，表示服务器端在执行请求时发生了错误
> 503 service unavailable，表明服务器暂时处于超负载或正在停机维护，无法处理请求

### 概括

#### Http特点

* 灵活可扩展，主要体现在两个方面。一个是语义上的自由，只规定了基本格式，比如空格分隔单词，换行分隔字段，其他的各个部分都没有严格的语法限制。另一个是传输形式的多样性，不仅仅可以传输文本，还能传输图片、视频等任意数据，非常方便。
* 可靠传输。Http 基于 TCP/IP，因此把这一特性继承了下来。这属于TCP的特性，不具体介绍了。
* 请求-应答。也就是一发一收、有来有回，当然这个请求方和应答方不单单指客户端和服务器之间，如果某台服务器作为代理来连接后端的服务端，那么这台服务器也会扮演请求方的角色。
* 无状态。这里的状态是指通信过程的上下文信息，而每次Http请求都是独立、无关的，默认不需要保留状态信息

#### Http缺点

* 无状态。所谓的优点和缺点还是要分场景来看的，对于Http而言，最具争议的地方在于它的无状态。在需要长连接的场景中，需要保存大量的上下文信息，以免传输大量重复的信息，那么这时候无状态就是Http的缺点了。但与此同时，另外一些应用仅仅只是为了获取一些数据，不需要保存连接上下文信息，无状态反而减少了网络开销，成为了Http的优点。
* 明文传输。即协议里的报文(主要指的是头部)不使用二进制数据，而是文本形式。
这当然对于调试提供了便利，但同时也让 Http 的报文信息暴露给了外界，给攻击者也提供了便利。WIFI陷阱就是利用 Http 明文传输的缺点，诱导你连上热点，然后疯狂抓你所有的流量，从而拿到你的敏感信息。
* 队头阻塞问题。当Http开启长连接时，共用一个 TCP 连接，同一时刻只能处理一个请求，那么当前请求耗时过长的情况下，其它的请求只能处于阻塞状态，也就是著名的队头阻塞问题。接下来会有一小节讨论这个问题。


### 参考文章
> https://juejin.im/post/5a0ce1d95188253e24708454
> https://juejin.im/post/5872309261ff4b005c4580d4
> https://juejin.im/post/5e76bd516fb9a07cce750746
