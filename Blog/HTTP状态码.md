## HTTP状态码分类
##### HTTP状态码由三个十进制数字组成，第一个十进制数字定义了状态码的类型，后两个数字没有分类的作用。HTTP状态码共分为5种类型：
> HTTP状态码分类

分类 | 分类描述
---|---
1**	|信息，服务器收到请求，需要请求者继续执行操作
2**	|成功，操作被成功接收并处理
3**	|重定向，需要进一步的操作以完成请求
4**	|客户端错误，请求包含语法错误或无法完成请求
5/6**	|服务器错误，服务器在处理请求的过程中发生了错误
#### 先介绍爬虫最常见的HTTP状态码
> 一些常见的HTTP状态码

状态码 | 状态码英文名称 | 我的理解
---|--- | ---
200 | OK | 出现200证明一切正常，皆大欢喜。
301 | Moved Permanently | 永久重定向。一般来说爬虫可以自适应，是不需要特别处理的。可以通过response中的url和status code来判断。如果没有问题可以不去处理。不过推荐通过response中的url或者抓包工具来将网页尽可能的直接改为跳转后的地址。因为重定向会使爬虫发两次request请求，白白占用带宽。
302 | Found | 临时重定向。处理方式同上↑↑↑↑↑↑
401 | Unauthorized | 出现这个状态码说明爬虫的登陆身份有问题。可能是登陆超时需要重新登陆，用户名密码错误，或者干脆没有登陆。需要重新处理爬虫的身份验证。
404 | Not Found | 无法找到指定位置的资源。大名鼎鼎的404错误。可能是爬虫访问的url中的资源被删除或者url错误。需要检查爬虫的爬到的url。
407 | Proxy Authentication Required | 代理验证错误。说明代理的登陆有问题。检查代理IP和账号密码。
500 | Internal Server Error | 服务器故障或Web应用故障。一般靠谱点的网站是不会出现这个问题的，这个通常是服务器在工作时发生了源代码error。出现了这个错误呢，可以通知站长需要维护网站了（大雾）。
503 | Service Unavailable | 503状态码的存在并不意味着服务器在过载的时候必须使用它。某些服务器只不过是希望拒绝客户端的连接。所以出现503时候，很大情况爬虫已经被反爬发现了，需要做反反爬试试。
#### 所有的HTTP状态码
> HTTP状态码列表

状态码 | 状态码英文名称 | 中文描述
--- | --- | --- | ---
100 | Continue | 继续。客户端应继续其请求
101 | Switching Protocols | 切换协议。服务器根据客户端的请求切换协议。只能切换到更高级的协议，例如，切换到HTTP的新版本协议
102 | Processing | 由WebDAV（RFC 2518）扩展的状态码，代表处理将被继续执行。
200 | OK| 请求成功。一般用于GET与POST请求
201 | Created | 已创建。成功请求并创建了新的资源
202 | Accepted | 已接受。已经接受请求，但未处理完成
203 | Non-Authoritative Information | 非授权信息。请求成功。但返回的meta信息不在原始的服务器，而是一个副本
204 | No Content | 无内容。服务器成功处理，但未返回内容。在未更新网页的情况下，可确保浏览器继续显示当前文档
205 | Reset Content | 重置内容。服务器处理成功，用户终端（例如：浏览器）应重置文档视图。可通过此返回码清除浏览器的表单域
206 | Partial Content | 部分内容。服务器成功处理了部分GET请求
207 | Multi-Status | 由WebDAV(RFC 2518)扩展的状态码，代表之后的消息体将是一个XML消息，并且可能依照之前子请求数量的不同，包含一系列独立的响应代码。
300 | Multiple Choices | 多种选择。请求的资源可包括多个位置，相应可返回一个资源特征与地址的列表用于用户终端（例如：浏览器）选择
301 | Moved Permanently | 永久移动。请求的资源已被永久的移动到新URI，返回信息会包括新的URI，浏览器会自动定向到新URI。今后任何新的请求都应使用新的URI代替
302 | Found | 临时移动。与301类似。但资源只是临时被移动。客户端应继续使用原有URI
303	| See Other | 查看其它地址。与301类似。使用GET和POST请求查看
304 | Not Modified | 未修改。所请求的资源未修改，服务器返回此状态码时，不会返回任何资源。客户端通常会缓存访问过的资源，通过提供一个头信息指出客户端希望只返回在指定日期之后修改的资源
305 | Use Proxy | 使用代理。所请求的资源必须通过代理访问
306 | Unused | 已经被废弃的HTTP状态码
307 | Temporary Redirect | 临时重定向。与302类似。使用GET请求重定向
400 | Bad Request | 客户端请求的语法错误，服务器无法理解
401 | Unauthorized | 请求要求用户的身份认证
402 | Payment Required | 保留，将来使用
403 | Forbidden | 服务器理解请求客户端的请求，但是拒绝执行此请求
404 | Not Found | 服务器无法根据客户端的请求找到资源（网页）。通过此代码，网站设计人员可设置"您所请求的资源无法找到"的个性页面
405	| Method Not Allowed | 客户端请求中的方法被禁止
406	| Not Acceptable | 服务器无法根据客户端请求的内容特性完成请求
407	| Proxy Authentication Required | 请求要求代理的身份认证，与401类似，但请求者应当使用代理进行授权
408 | Request Time-out | 服务器等待客户端发送的请求时间过长，超时
409 | Conflict| 服务器完成客户端的PUT请求是可能返回此代码，服务器处理请求时发生了冲突
410 | Gone | 客户端请求的资源已经不存在。410不同于404，如果资源以前有现在被永久删除了可使用410代码，网站设计人员可通过301代码指定资源的新位置
411 | Length Required | 服务器无法处理客户端发送的不带Content-Length的请求信息
412 | Precondition Failed | 客户端请求信息的先决条件错误
413 | Request Entity Too Large | 由于请求的实体过大，服务器无法处理，因此拒绝请求。为防止客户端的连续请求，服务器可能会关闭连接。如果只是服务器暂时无法处理，则会包含一个Retry-After的响应信息
414 | Request-URI Too Large | 请求的URI过长（URI通常为网址），服务器无法处理
415 | Unsupported Media Type | 服务器无法处理请求附带的媒体格式
416 | Requested range not satisfiable | 客户端请求的范围无效
417 | Expectation Failed | 服务器无法满足Expect的请求头信息
421 | too many connections| There are too many connections from your internet address从当前客户端所在的IP地址到服务器的连接数超过了服务器许可的最大范围。通常，这里的IP地址指的是从服务器上看到的客户端地址（比如用户的网关或者代理服务器地址）。在这种情况下，连接数的计算可能涉及到不止一个终端用户。
422 | Unprocessable Entity | 请求格式正确，但是由于含有语义错误，无法响应。（RFC 4918 WebDAV）
423 | Locked | 当前资源被锁定。（RFC 4918 WebDAV）
424 | Failed Dependency | 由于之前的某个请求发生的错误，导致当前请求失败，例如 PROPPATCH。（RFC 4918 WebDAV）
425 | Unordered Collection | 在WebDav Advanced Collections 草案中定义，但是未出现在《WebDAV 顺序集协议》（RFC 3658）中。
426 | Upgrade Required | 客户端应当切换到TLS/1.0。（RFC 2817）
449 | Retry With | 由微软扩展，代表请求应当在执行完适当的操作后进行重试。
451 | Unavailable For Legal Reasons | 该请求因法律原因不可用。（RFC 7725）
500 | Internal Server Error | 服务器内部错误，无法完成请求
501 | Not Implemented |服务器不支持请求的功能，无法完成请求
502 | Bad Gateway | 充当网关或代理的服务器，从远端服务器接收到了一个无效的请求
503 | Service Unavailable | 由于超载或系统维护，服务器暂时的无法处理客户端的请求。延时的长度可包含在服务器的Retry-After头信息中
504 | Gateway Time-out| 充当网关或代理的服务器，未及时从远端服务器获取请求
505 | HTTP Version not supported | 服务器不支持请求的HTTP协议的版本，无法完成处理
506 | Variant Also Negotiates | 由《透明内容协商协议》（RFC 2295）扩展，代表服务器存在内部配置错误：被请求的协商变元资源被配置为在透明内容协商中使用自己，因此在一个协商处理中不是一个合适的重点。
507 | Insufficient Storage | 服务器无法存储完成请求所必须的内容。这个状况被认为是临时的。WebDAV (RFC 4918)
509 | Bandwidth Limit Exceeded | 服务器达到带宽限制。这不是一个官方的状态码，但是仍被广泛使用。
510 | Not Extended | 获取资源所需要的策略并没有被满足。（RFC 2774）
600 | Unparseable Response Headers | 源站没有返回响应头部，只返回实体内容   

参考文献：[HTTP状态码 | 菜鸟教程](http://www.runoob.com/http/http-status-codes.html)